## FC在设计上践行了代数效应的思想

>React.FC是一个函数式组件，是在TypeScript使用一个泛型，FC就是FunctionComponent的缩写，事实上React.FC可以写成React.FunctionComponent


只要遵循一定规范，在FC中可以不区分同步、异步、以同样的方式从数据源中获取数据.


代数效应在React中的应用需要Suspense来处理“视图的pending”状态

1. React.lazy

    在使用webpack等工具动态import时，加载动态引入的组件会发起一个jsonp请求，**当请求返回前**，UI的中间状态会交由组件树中抽离“动态引入的组件”最近的Suspense处理。

2. startTransition 和 useTransition
    
    有时候我们切换状态的时候，我们想先保留旧组件的UI，这时可以使用startTransition来降低“本次更新的优先级”，React会保留旧的UI，并等待新的UI完成。


3. Server Components

    Server Components获取数据后，会在服务端与客户端以“序列化的JSX格式”流式传输。传输过程中UI的中间状态交由Suspense处理


4. Selective Hydration（选择性注水）

    Hydration的缺点

    1. 页面中不同部分“展示优先级”是有差异的，但是Hydration一视同仁。
    2. 整个应用Hydration工作完成后，UI的任意部分才能交互。

    Selective Hydration解决上面的问题。 选择性由Suspense来实现。

    1. 为Hydrate划分粒度，高优先级部分优先展示

    2. 被Suspense包裹的组件可以在整个应用Hydrate完成前响应交互。使“产生用户交互”的部分优先Hydrate。







