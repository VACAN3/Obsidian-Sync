### 1. SSE 方法文档

#### 功能特色

1. **自动重连机制**：在连接中断时，自动尝试重新连接，无需手动干预，提高了应用的稳定性和用户体验。
2. **超时处理**：支持自定义超时时间，如果在指定时间内没有接收到任何数据，将自动触发重连机制。
3. **状态管理集成**：集成了 Pinia 状态管理，方便在全局状态中管理和访问 SSE 接收到的数据。
4. **错误处理**：对 SSE 连接和数据处理中的错误进行捕获和处理，保证应用的健壮性。
5. **类型安全**：使用 TypeScript 进行强类型定义，提高代码的可维护性和减少运行时错误。

#### 使用方法

##### 在业务组件中引入和使用

1. **引入 SSE 方法**：
   在组件中引入 `useSSE` 方法，这个方法已经在全局进行了挂载，可以直接通过 `this` 访问。

   ```typescript
   import { defineComponent, onUnmounted } from 'vue';

   export default defineComponent({
     setup() {
       const handleSSEMessage = async (event) => {
         console.log('Received SSE message:', event.data);
         // await updateUI(event.data); // 同步更新状态/UI
       };

       const stopSSE = this.useSSE('https://example.com/sse', handleSSEMessage, {
         timeout: 5000,
         reconnectInterval: 10000
       });

       onUnmounted(() => {
         stopSSE();
       });

       return {};
     }
   });
   ```

2. **配置 SSE**：
   - `url`: SSE 服务器的 URL。
   - `onMessage`: 接收到消息时的回调函数。
   - `options`: 可选配置，包括：
     - `headers`: 自定义请求头。
     - `timeout`: 超时时间，单位为毫秒。
     - `reconnectInterval`: 重连间隔时间，单位为毫秒。

3. **处理 SSE 消息**：
   在 `onMessage` 回调中，你可以处理接收到的 SSE 消息。例如，更新视图、存储数据等。

4. **停止 SSE**：
   `useSSE` 方法返回一个停止函数，可以在组件卸载时调用，以清理和停止 SSE 连接。

##### 全局方法挂载

在 `main.ts` 中，`useSSE` 已经被添加到 Vue 实例的全局属性中，因此在任何组件内部都可以通过 `this.useSSE` 访问到这个方法。

```typescript
app.config.globalProperties.useSSE = useSSE;
```

在 `module.d.ts` 中，确保 `useSSE` 方法的类型声明正确，以便在 TypeScript 项目中获得类型检查和自动完成的支持。

```typescript
declare module '@vue/runtime-core' {
  interface ComponentCustomProperties {
    useSSE: typeof useSSE;
  }
}
```

通过这种方式，`useSSE` 方法可以在 Vue 3 的 Composition API 或者 Options API 中灵活使用，提供了强大的实时数据处理能力。




### 2. 具体实现
1. 