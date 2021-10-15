## [setState](https://juejin.cn/post/6844904015524790279)

### 进入更新队列

1. setState 函数：

   ```json
   Component.prototype.setState = function(partialState, callback) {
     this.updater.enqueueSetState(this, partialState, callback, 'setState');
   };
   // 将partialState和callback加入更新队列
   ```

2. enqueueSetState函数

   ```js
   enqueueSetState(inst, payload, callback) {
     const fiber = getInstance(inst);
     const currentTime = requestCurrentTime();
     const suspenseConfig = requestCurrentSuspenseConfig();
     const expirationTime = computeExpirationForFiber(
       currentTime,
       fiber,
       suspenseConfig,
     );
   
     // 创建一个update对象
     const update = createUpdate(expirationTime, suspenseConfig);
     // payload存放的是要更新的状态，即partialState
     update.payload = payload;
   
     // 如果定义了callback，则将callback挂载在update对象上
     if (callback !== undefined && callback !== null) {
       update.callback = callback;
     }
   
     // ...省略...
   
     // 将update对象添加至更新队列中
     enqueueUpdate(fiber, update);
     // 添加调度任务
     scheduleWork(fiber, expirationTime);
   },
   // 创建多个update，依次放入更新队列，并依次添加调度任务
   ```

3. createUpdate

   ```js
   export function createUpdate(
     expirationTime: ExpirationTime,
     suspenseConfig: null | SuspenseConfig,
   ): Update<*> {
     let update: Update<*> = {
       expirationTime,
       suspenseConfig,
   
       // 添加TAG标识，表示当前操作是UpdateState，后续会用到。
       tag: UpdateState,
       payload: null,
       callback: null,
   
       next: null,
       nextEffect: null,
     };
   
     return update;
   }
   
   ```

   

### 状态更新机制

1. getStateFromUpdate

   ```js
   function getStateFromUpdate<State>(
     workInProgress: Fiber,
     queue: UpdateQueue<State>,
     update: Update<State>,
     prevState: State,
     nextProps: any,
     instance: any,
   ): any {
     switch (update.tag) {
   
       // ....省略 ....
   
       // 见3.3节内容，调用setState会创建update对象，其属性tag当时被标记为UpdateState
       case UpdateState: {
         // payload 存放的是要更新的状态state
         const payload = update.payload;
         let partialState;
   
         // 获取要更新的状态
         if (typeof payload === 'function') {
           partialState = payload.call(instance, prevState, nextProps);
         } else {
           partialState = payload;
         }
   
         // partialState 为null 或者 undefined，则视为未操作，返回上次状态
         if (partialState === null || partialState === undefined) {
           return prevState;
         }
   
         // 注意：此处通过Object.assign生成一个全新的状态state， state的引用地址发生了变化。
         return Object.assign({}, prevState, partialState);
       }
   
       // .... 省略 ....
     }
   
     return prevState;
   }
   // 将partialState和preState合并
   // Object.assign第一次参数为控对象，代表新state引用地址会改变
   // Object.assign是浅拷贝
   ```

2. 合并一次render前的所有state

   ```js
   export function processUpdateQueue<State>(
     workInProgress: Fiber,
     queue: UpdateQueue<State>,
     props: any,
     instance: any,
     renderExpirationTime: ExpirationTime,
   ): void {
     // ...省略...
   
     // 获取上次状态prevState
     let newBaseState = queue.baseState;
   
     /**
      * 若在render之前多次调用了setState，则会产生多个update对象。这些update对象会以链表的形式存在queue中。
      * 现在对这个更新队列进行依次遍历，并计算出最终要更新的状态state。
      */
     let update = queue.firstUpdate;
     let resultState = newBaseState;
     while (update !== null) {
       // ...省略...
   
       /**
        * resultState作为参数prevState传入getStateFromUpdate，然后getStateFromUpdate会合并生成
        * 新的状态再次赋值给resultState。完成整个循环遍历，resultState即为最终要更新的state。
        */
       resultState = getStateFromUpdate(
         workInProgress,
         queue,
         update,
         resultState,
         props,
         instance,
       );
       // ...省略...
   
       // 遍历下一个update对象
       update = update.next;
     }
   
     // ...省略...
   
     // 将处理后的resultState更新到workInProgess上
     workInProgress.memoizedState = resultState;
   }
   
   // 循环update，合并partialState和preState,update = update.next循环完为止
   ```

3. checkShouldComponentUpdate

   ```js
   function updateClassInstance(
     current: Fiber,
     workInProgress: Fiber,
     ctor: any,
     newProps: any,
     renderExpirationTime: ExpirationTime,
   ): boolean {
     // 获取当前实例
     const instance = workInProgress.stateNode;
   
     // ...省略...
   
     const oldState = workInProgress.memoizedState;
     let newState = (instance.state = oldState);
     let updateQueue = workInProgress.updateQueue;
   
     // 如果更新队列不为空，则处理更新队列，并将最终要更新的state赋值给newState
     if (updateQueue !== null) {
       processUpdateQueue(
         workInProgress,
         updateQueue,
         newProps,
         instance,
         renderExpirationTime,
       );
       newState = workInProgress.memoizedState;
     }
   
     // ...省略...
   
     /**
      * shouldUpdate用于标识组件是否要进行渲染，其值取决于组件的shouldComponentUpdate生命周期执行结果，
      * 亦或者PureComponent的浅比较的返回结果。
      */
     const shouldUpdate = checkShouldComponentUpdate(
         workInProgress,
         ctor,
         oldProps,
         newProps,
         oldState,
         newState,
         nextContext,
       );
   
     if (shouldUpdate) {
        // 如果需要更新，则执行相应的生命周期函数
        if (typeof instance.UNSAFE_componentWillUpdate === 'function' ||
           typeof instance.componentWillUpdate === 'function') {
         startPhaseTimer(workInProgress, 'componentWillUpdate');
         if (typeof instance.componentWillUpdate === 'function') {
           instance.componentWillUpdate(newProps, newState, nextContext);
         }
         if (typeof instance.UNSAFE_componentWillUpdate === 'function') {
           instance.UNSAFE_componentWillUpdate(newProps, newState, nextContext);
         }
         stopPhaseTimer();
       }
       // ...省略...
     }
   
     // ...省略...
   
     /**
      * 不管shouldUpdate的值是true还是false，都会更新当前组件实例的props和state的值，
      * 即组件实例的state和props的引用地址发生变化。也就是说即使我们采用PureComponent来减少无用渲染，
      * 但并不代表该组件的state或者props的引用地址没有发生变化！！！
      */
     instance.props = newProps;
     instance.state = newState;
   
     return shouldUpdate;
   }
   
   // 获取shouldUpdate，调用componentWillUpdate函数，赋值实例新的props和state，返回shouldUpdate用以判断是否需要渲染
   ```

4. updateClassComponent函数

   ```js
   function updateClassComponent(
     current: Fiber | null,
     workInProgress: Fiber,
     Component: any,
     nextProps,
     renderExpirationTime: ExpirationTime,
   ) {
     // 获取组件实例
     const instance = workInProgress.stateNode;
   
     // ...省略...
   
     let shouldUpdate;
   
     /**
      * 1. 完成组件实例的state、props的更新;
      * 2. componentWillUpdate、shouldComponentUpdate生命周期函数执行完毕；
      * 3. 获取是否要进行更新的标识shouldUpdate；
      */
     shouldUpdate = updateClassInstance(
       current,
       workInProgress,
       Component,
       nextProps,
       renderExpirationTime,
     );
   
     /**
      * 1. 如果shouldUpdate值为false，则退出渲染；
      * 2. 执行render函数
      */
     const nextUnitOfWork = finishClassComponent(
       current,
       workInProgress,
       Component,
       shouldUpdate,
       hasContext,
       renderExpirationTime,
     );
   
     // 返回下一个任务单元
     return nextUnitOfWork;
   }
   
   // 完成实例props，state更新；执行componentWillUpdate/shouldComponentUpdate生命周期；根据shouldUpdate执行渲染；返回下一个任务单元
   ```

   

### 总结

1. 每一个setState都创建一个update，update方式更新队列，和给调度队列增加任务
2. 循环更新队列，将每个update里的state和preState 合并
3. 将合并后新的state和props赋给实例
4. 将state和props给componentWillupdate函数
5. 获取shouldUpdate（purecomponent或shouldComponentUpdate的结果）
6. 根据shouldUpdate去判断是否要重新render组件