1. sse通道每次只返回一条新增的数据，用于弹窗提示。
	1. 注意点：
		1. 该提示不会自动消失，多条数据叠加，最顶层的那条通知右上角显示“一键清除”按钮，清楚当前所有弹窗。
		2. ✕ 按钮只是关闭弹窗，无法已读。
	2. 点击弹窗，弹出站内信详情弹窗，后续方式沿用现有逻辑。
	3. 数据结构如下：
``` javascript
{
    "msgType": "site_inbox_unread",
    "userId": "0", // 用户id
    "total": 0, // 站内信 未读数量
    // siteInboxMsg 对象与接口：/system/msg/push/listUserUnReadSitInboxMsgList 返回 rows 的 对象保持一致
    "siteInboxMsg":{
        "id": 0, // 站内信用户接受主键
        "msgId": 0, // 消息主键
        "msgTitle": "", // 消息标题
        "msgBody": "", // 消息内容（富文本）
        "remark": "", // 备注
        "msgCreateBy": 0, // 消息创建人
        "msgCreateByNickName": "", // 消息创建人用户名称
        "createTime": "", // 消息创建时间
        "receiveUserId": 0, // 消息接受用户id
        "receiveUserNickName": "", // 用户名称
        "pushStatus": "", // 推送状态
        "pushTime": "", // 推送时间
        "receiveStatus": "", // 接受状态
        "receiveTime": "" // 接受的时间
    }
    "date": "yyyy-MM-dd HH:mm:ss" // 消息时间
}
```

2. 登录进入系统后，首次获取通知公告列表数据，用户滚动分页加载数据。如果sse有接收到新的信息，则把那条数据加入到列表页数据的最顶部（第一条）。
	1. 注意点：
		1. 滚动加载下一页数据时，需要对获取到的数据进行去重处理。
		2. 点击通知公告的某条数据时，该数据从未读变为已读，隔三秒后自动消失。此时需要考虑是否需要新数据顶上来的交互。
3. sse通道获取到的新消息存入pinia，