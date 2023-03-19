# Suspense工作流程

1. mount时，此时suspend状态,组件抛出一个promise状态

    1. 寻找fiber tree中距离抛出错误的fiber node最近的 Suspense 对应的 fibernode ，并为它标记 should capture flag

    2. 等待 promise状态变化，在then回调中触发新的一轮的更新的同时

        进入 unwind流程，主要目的，捕获错误后显示fallback UI

        
        开始于发生错误的fiberNode，从这个组件向上遍历

        找到符合条件的Suspense或ErrorBoundary，会将对应的fibernode返回，终止unwind流程
2. 进入suspend状态状态，渲染fallback UI

3. promise状态变化，请求成功，then回调执行，重新触发更新，组件进入render流程。


## 总结

suspense会经历三次benginwork

1. mount时

2. unwind流程

3. promise返回状态后

