|              | function                     | class                                                        |
| ------------ | ---------------------------- | ------------------------------------------------------------ |
| 编译速度     | 少，没有继承，不用多转es5    |                                                              |
| 代码量       | 少，没有继承                 |                                                              |
| 测试         | 简单，没有复杂的生命周期     |                                                              |
| this的影响   | 会闭包，保留值               | 随props，state变化                                           |
| 复杂状态管理 | useState保存的数据结相对简单 | 可以setState和batch（setState的callback接收到state和props）复杂数据接口 |
| 复杂情景处理 | 需要去拼hooks模拟生命周期    | 有完整生命周期                                               |
|              |                              | PureComponent 和shouldComponentUpdate                        |
|              |                              |                                                              |



## Function component

```
useState: 加入元件狀態，但這裡的 setState 不會幫我們自動合併物件型態的狀態，需要用 callback 方式寫並且自行合併
useReducer: 可以用來處理物件型態的狀態
useEffect: 處理 side effect，取代 componentDidMount, componentDidUpdate, componentWillUnmount
useCallback: 當 function 需要在 useEffect 中被使用但又不想加入觸發條件
useMemo: 把較高成本計算記起來
useRef: 取得參考用的 object
React.memo: 可以當成 Functional 的 Pure Component
```

hooks具有capture value的特性

用useRef代替useState可以避免这个特性



## 生命周期

#### componentDidMount

```react
componentDidMount() {
  fetchData();
}

useEffect( () => { fetchData(); }, [] );


```



#### componentWillUnmount

```react
componentDidUpdate(prevProps) {
  // 常見用法（別忘了比較 prop）：
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}

useEffect( () => { fetchData(userID); }, [userID] );


```

#### componentDidUpdate

```react
const mounting = useRef(true);
useEffect(() => {
  if (mounting.current) {
    mounting.current = false;
  } else {
    fn();
  }
});
```

 

#### forceUpdate

```react
const [ignored, forceUpdate] = useReducer(x => x + 1, 0);

function handleClick() {
  forceUpdate();
}
```



#### 获取上一个props

```react
function Counter() {
  const [count, setCount] = useState(0);
  const prevCount = usePrevious(count);
  return (
    <h1>
      Now: {count}, before: {prevCount}
    </h1>
  );
}

function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;
}
```



#### useState技巧

```react
// useState 函数的参数虽然是初始值，但由于整个函数都是 Render，因此每次初始化都会被调用，如果初始值计算非常消耗时间，建议使用函数传入，这样只会执行一次：

function FunctionComponent(props) {
  const [rows, setRows] = useState(() => createRows(props.count));
}
```





## 参考资料

1. https://linyencheng.github.io/2020/02/02/react-component-class-based-vs-functional/
2. https://zhuanlan.zhihu.com/p/59558396

3. https://www.jianshu.com/p/9c9413a7ea61 React 如何区别class和function？
4. https://blog.techbridge.cc/2020/06/13/class-function-component-and-useeffect/ 从实际案例看React Class component和Function component的问题

