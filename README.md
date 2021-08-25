# Function vs Class

|              | Function component   | Class component                  |
| ------------ | -------------------- | -------------------------------- |
| 编译快       | 少了继承class装成es6 |                                  |
| 更少代码     | 没有继承             |                                  |
| 测试容易     | 生命周期少           |                                  |
| this的影响   | 闭包会抓值           | this.props(state)会改变          |
| 复杂状态处理 |                      | setState：设置多个状态会自动合并 |
| 复杂的情景   |                      | 有完善的生命周期                 |



### Function component 模拟生命周期

```jsx
// componentDidMount()
componentDidMount(){
  fetchData()
}

useEffect(()=>{fetchData()},[])
```

```jsx
// componentDidUpdate
componentDidUpdate(prevProps) {
  // 常見用法（別忘了比較 prop）：
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}

// 1
useEffect( () => { fetchData(userID); }, [userID] )

// 2
const mounting = useRef(true);
useEffect(() => {
  if (mounting.current) {
    mounting.current = false;
  } else {
    fn();
  }
});
```

```jsx
// componentWillUnmount
componentWillUnmount() {
  clear();
}
useEffect(() => {

  return () => {
      clear();
  };
}, []);
```

```jsx
// shouldComponentUpdate
const Button = React.memo(props => {
  // your component
});

function Parent({ a, b }) {
  // Only re-rendered if `a` changes:
  const child1 = useMemo(() => <Child1 a={a} />, [a]);
  // Only re-rendered if `b` changes:
  const child2 = useMemo(() => <Child2 b={b} />, [b]);
  return (
    <>
      {child1}
      {child2}
    </>
  );
}
```

```jsx
// forceUpdate

const [ignored, forceUpdate] = useReducer(x => x + 1, 0);

function handleClick() {
  forceUpdate();
}
```

```jsx
// state 拆分合并
setState(state => ({ ...state, left: e.pageX, top: e.pageY }));

```

```jsx
// 获取上一次状态
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

