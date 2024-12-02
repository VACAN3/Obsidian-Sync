```
// request.ts

import axios, { AxiosResponse, InternalAxiosRequestConfig } from 'axios';
import { useUserStore } from '@/store/modules/user';
import { getToken } from '@/utils/auth';
import { tansParams, blobValidate } from '@/utils/hmdp';
import cache from '@/plugins/cache';
import { HttpStatus } from '@/enums/RespEnum';
import { errorCode } from '@/utils/errorCode';
import { LoadingInstance } from 'element-plus/es/components/loading/src/loading';
import FileSaver from 'file-saver';
import { getLanguage } from '@/lang';
import { encryptBase64, encryptWithAes, generateAesKey, decryptWithAes, decryptBase64 } from '@/utils/crypto';
import { encrypt, decrypt } from '@/utils/jsencrypt';
import { downloadBlob } from '@/utils';
import {
	EventEmitter, generateReqKey,
	handleSuccessResponse_limit,
	handleErrorResponse_limit,
	isFileUploadApi
} from './preventDuplication';
interface ExtendedConfig extends InternalAxiosRequestConfig {
	pendKey?: string;  // 假设 pendKey 是字符串类型
}
const encryptHeader = 'encrypt-key';
let downloadLoadingInstance: LoadingInstance;
// 是否显示重新登录
export const isRelogin = { show: false };
export const globalHeaders = () => {
	return {
		Authorization: 'Bearer ' + getToken(),
		clientid: import.meta.env.VITE_APP_CLIENT_ID,
	};
};

axios.defaults.headers['Content-Type'] = 'application/json;charset=utf-8';
axios.defaults.headers['clientid'] = import.meta.env.VITE_APP_CLIENT_ID;
// 创建 axios 实例
const service = axios.create({
	baseURL: import.meta.env.VITE_APP_BASE_API,
	timeout: 50000,
});
// 存储已发送但未响应的请求
const pendingRequest = new Set();
// 发布订阅容器
const ev = new EventEmitter()
// 请求拦截器
service.interceptors.request.use(
	async (config: ExtendedConfig) => {
		let hash = location.pathname
		// 生成请求Key
		let reqKey = generateReqKey(config, hash)
		// 对应国际化资源文件后缀
		config.headers['Content-Language'] = getLanguage();

		const isToken = (config.headers || {}).isToken === false;
		// 是否需要防止数据重复提交
		const isRepeatSubmit = (config.headers || {}).repeatSubmit === false;
		// 是否需要加密
		const isEncrypt = (config.headers || {}).isEncrypt === 'true';
		if (getToken() && !isToken) {
			config.headers['Authorization'] = 'Bearer ' + getToken(); // 让每个请求携带自定义token 请根据实际情况自行修改
		}
		// get请求映射params参数
		if (config.method === 'get' && config.params) {
			let url = config.url + '?' + tansParams(config.params);
			url = url.slice(0, -1);
			config.params = {};
			config.url = url;
		}
		// 当开启参数加密
		if (isEncrypt && (config.method === 'post' || config.method === 'put')) {
			// 生成一个 AES 密钥
			const aesKey = generateAesKey();
			config.headers[encryptHeader] = encrypt(encryptBase64(aesKey));
			config.data = typeof config.data === 'object' ? encryptWithAes(JSON.stringify(config.data), aesKey) : encryptWithAes(config.data, aesKey);
		}
		// FormData数据去请求头Content-Type
		if (config.data instanceof FormData) {
			delete config.headers['Content-Type'];
		}
		//console.log('请求拦截', isFileUploadApi(config))
		if (!isFileUploadApi(config)) {
			if (pendingRequest.has(reqKey)) {
				// 如果是相同请求,在这里将请求挂起，通过发布订阅来为该请求返回结果
				// 这里需注意，拿到结果后，无论成功与否，都需要return Promise.reject()来中断这次请求，否则请求会正常发送至服务器
				let res = null

				const controller = new AbortController();
				config.signal = controller.signal;

				try {
					// 接口成功响应
					res = await new Promise((resolve, reject) => {
						ev.on(reqKey, resolve, reject)
					})
					
					controller.abort(); // 取消请求

					return Promise.reject({
						type: 'limiteResSuccess',
						val: res
					})
				} catch (limitFunErr) {
					// 接口报错
					
					controller.abort();

					return Promise.reject({
						type: 'limiteResError',
						val: limitFunErr
					})
				}
			} else {
				// 将请求的key保存在config
				config['pendKey'] = reqKey
				pendingRequest.add(reqKey)
			}
		}
		return config;
	},
	(error: any) => {
		console.log(error);
		return Promise.reject(error);
	}
);

// 响应拦截器
service.interceptors.response.use(
	(res: any) => {
		// 加密后的 AES 秘钥
		const keyStr = res.headers[encryptHeader];
		// 加密
		if (keyStr != null && keyStr != '') {
			const data = res.data;
			// 请求体 AES 解密
			const base64Str = decrypt(keyStr);
			// base64 解码 得到请求头的 AES 秘钥
			const aesKey = decryptBase64(base64Str.toString());
			// aesKey 解码 data
			const decryptData = decryptWithAes(data, aesKey);
			// 将结果 (得到的是 JSON 字符串) 转为 JSON
			res.data = JSON.parse(decryptData);
		}
		// 未设置状态码则默认成功状态
		const code = res.data.code || HttpStatus.SUCCESS;
		// 获取错误信息
		const msg = errorCode[code] || res.data.msg || errorCode['default'];
		// 二进制数据则直接转为文件下载
		if (res.request.responseType === 'blob' || res.request.responseType === 'arraybuffer') {
			const name = res.headers['download-filename'];
			if (res.headers && res.headers.isasync === 'true') {
				ElMessageBox.alert("导出文件过大，已自动创建生成任务，请到<a style='color: #1515FF; font-weight: bold;' href='/hmdp/downloadCenter'> 下载中心 </a>进行下载。", '系统提示', {
					confirmButtonText: '关闭',
					type: 'warning',
					dangerouslyUseHTMLString: true,
				});
				return;
			}

			const fileName = decodeURI(name) || 'excel.xlsx';
			downloadBlob(res.data, fileName);
		}
		if (code === 401) {
			// prettier-ignore
			if (!isRelogin.show) {
				isRelogin.show = true;
				ElMessageBox.confirm('登录状态已过期，您可以继续留在该页面，或者重新登录', '系统提示', {
					confirmButtonText: '重新登录',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
					isRelogin.show = false;
					useUserStore().logout().then(() => {
						location.href = import.meta.env.VITE_APP_CONTEXT_PATH + 'index';
					});
				}).catch(() => {
					isRelogin.show = false;
				});
			}
			return Promise.reject('无效的会话，或者会话已过期，请重新登录。');
		} else if (code === HttpStatus.SERVER_ERROR) {
			console.log(msg);
			const config: any = res.config || {};
			if (!config.suppressError) ElMessage({ message: msg, type: 'error' });
			return Promise.reject(new Error(msg));
		} else if (code === HttpStatus.WARN) {
			ElMessage({ message: msg, type: 'warning' });
			return Promise.reject(new Error(msg));
		} else if (code !== HttpStatus.SUCCESS) {
			ElNotification.error({ title: msg });
			return Promise.reject('error');
		} else {
			// 将拿到的结果发布给其他相同的接口
			//console.log('请求成功', isFileUploadApi(res))
			if (!isFileUploadApi(res)) {
				handleSuccessResponse_limit(res, pendingRequest, ev)
			}

			return Promise.resolve(res.data);
		}
	},
	(error: any) => {
		console.log('error', error);
		let { message } = error;
		if (message == 'Network Error') {
			message = '后端接口连接异常';
		} else if (message.includes('timeout')) {
			message = '系统接口请求超时';
		} else if (message.includes('Request failed with status code')) {
			message = '系统接口' + message.substr(message.length - 3) + '异常';
		}
		ElMessage({ message: message, type: 'error', duration: 5 * 1000 });
		return handleErrorResponse_limit(error, pendingRequest, ev)
	}
);
// 通用下载方法
export function download(url: string, params: any, fileName: string) {
	downloadLoadingInstance = ElLoading.service({ text: '正在下载数据，请稍候', background: 'rgba(0, 0, 0, 0.7)' });
	// prettier-ignore
	return service.post(url, params, {
		transformRequest: [
			(params: any) => {
				return tansParams(params);
			}
		],
		headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
		responseType: 'blob'
	}).then(async (resp: any) => {
		const isLogin = blobValidate(resp);
		if (isLogin) {
			const blob = new Blob([resp]);
			FileSaver.saveAs(blob, fileName);
		} else {
			const resText = await resp.data.text();
			const rspObj = JSON.parse(resText);
			const errMsg = errorCode[rspObj.code] || rspObj.msg || errorCode['default'];
			ElMessage.error(errMsg);
		}
		downloadLoadingInstance.close();
	}).catch((r: any) => {
		console.error(r);
		ElMessage.error('下载文件出现错误，请联系管理员！');
		downloadLoadingInstance.close();
	});
}
// 导出 axios 实例
export default service;

```


```
// preventDuplication.js

// 发布订阅
export class EventEmitter {
    constructor() {
        this.event = {}
    }
    on(type, cbres, cbrej) {
        if (!this.event[type]) {
            this.event[type] = [[cbres, cbrej]]
        } else {
            this.event[type].push([cbres, cbrej])
        }
    }

    emit(type, res, ansType) {
        if (!this.event[type]) return
        else {
            this.event[type].forEach(cbArr => {
                if (ansType === 'resolve') {
                    cbArr[0](res)
                } else {
                    cbArr[1](res)
                }
            });
        }
    }
}


// 根据请求生成对应的key
export function generateReqKey(config, hash) {
    const { method, url, params, data } = config;
    return [method, url, JSON.stringify(params), JSON.stringify(data), hash].join("&");
}
// 接口响应成功
export function handleSuccessResponse_limit(response, pendingRequest, ev) {
    const reqKey = response.config.pendKey
    if (pendingRequest.has(reqKey)) {
        let x = null
        try {
            x = JSON.parse(JSON.stringify(response))
        } catch (e) {
            x = response
        }
        pendingRequest.delete(reqKey)
        ev.emit(reqKey, x, 'resolve')
        //  console.log('请求成功', reqKey)
        delete ev.reqKey
    }
}

// 接口走失败响应
export function handleErrorResponse_limit(error, pendingRequest, ev) {
    if (error.type && error.type === 'limiteResSuccess') {
        return Promise.resolve(error.val)
    } else if (error.type && error.type === 'limiteResError') {
        return Promise.reject(error.val);
    } else {
        const reqKey = error.config.pendKey
        if (pendingRequest.has(reqKey)) {
            let x = null
            try {
                x = JSON.parse(JSON.stringify(error))
            } catch (e) {
                x = error
            }
            pendingRequest.delete(reqKey)
            ev.emit(reqKey, x, 'reject')
            delete ev.reqKey
        }
    }
    return Promise.reject(error);
}
// 判断是否是文件上传接口
export function isFileUploadApi(config) {
    return Object.prototype.toString.call(config.data) === "[object FormData]"
}
```



### 如何理解此文件中发布订阅容器的用法？以及如何理解“将拿到的结果发布给其他相同的接口”这句话？

在您提供的代码中，发布订阅模式通过 `EventEmitter` 类实现，这是一种允许不同代码组件之间进行通信的设计模式，而无需这些组件直接引用彼此。这种模式非常适合处理异步编程场景，尤其是在处理多个请求和响应时，可以避免重复请求和冲突。
#### 发布订阅容器的用法
1. **定义事件容器**：`EventEmitter` 类中有一个 `event` 对象，用于存储所有的事件及其对应的处理函数（回调函数）。每个事件可以有多个订阅者。
2. **订阅事件**：通过 `on` 方法，可以为特定类型的事件添加一个或多个处理函数。这些处理函数在事件被触发时调用。
3. **触发事件**：通过 `emit` 方法，当某个事件发生时（例如，API响应返回），会查找该事件类型的所有订阅者，并逐一执行对应的处理函数。`emit` 方法还区分处理函数是解决（resolve）还是拒绝（reject）的情况。
#### “将拿到的结果发布给其他相同的接口”解释
这句话的意思是，当一个API请求完成并且得到响应后，系统不仅会处理这个响应，还会检查是否有其他相同的请求正在等待这个响应。如果有，它会使用发布订阅模式将这个响应结果“发布”给所有等待这个相同请求的处理函数。
这在处理重复请求时非常有用，尤其是在高并发环境下，可以避免服务器对相同数据的多次处理，提高效率和性能。例如，如果多个用户几乎同时请求同一数据，服务器只需处理一次请求，然后将结果分发给所有请求者。

#### 代码实现
在 `request.ts` 文件中，请求拦截器会检查是否有相同的请求已经在处理中（通过 `pendingRequest` 集合）。如果是，新的请求将不会发送到服务器，而是等待原始请求的结果。一旦原始请求完成，它的结果会通过 `EventEmitter` 发布出去，所有挂起的相同请求都会接收到这个结果，并处理它。
这种方法有效地减少了不必要的网络负载和服务器处理，同时确保所有请求者都能及时获得所需数据。
