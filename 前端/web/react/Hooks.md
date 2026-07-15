# Hooks

- Hook是React16.8的新增特性
- 可以让函数式组件拥有class组件一样的状态和钩子（生命周期）
- 完全是可选的，不包含任何破坏性改动
- Hooks就是很多个Hook
- 不能在判断和循环中使用

# 解决的问题(动机)

解决编写和维护函数式组件和class组件时遇到的各种看起来不相关的问题

##### 1、在组件之间复用状态逻辑很难

- providers、consumers、高阶组件、render props等其他抽象层组成的组件会形成“嵌套地狱”。
- React需要为共享状态逻辑提供更好的原生途径
- 可以使用hook从组件中提取状态逻辑，使这些逻辑可以单独复用。
- 可以在无需修改组件结构的情况下复用状态逻辑，使得在组件间和社区共享hook变得更加快捷

##### 2、复杂组件变得难以理解

- 每个生命周期包含了不相关的逻辑（操作dom、发动请求，json数据处理）
- 容易产生bug，导致逻辑不一致（class组件存在的问题）
- 为了解决这个问题，hook将组件中相关联的部分拆分成更小的函数

##### 3、难以理解的class

- React 组件一只更像是函数，hook则拥抱了函数。

# useState

- `[属性，方法] = useState(默认值)`
- useState可以让函数式组件像class一样拥有状态

```js
function App() {
  // [属性，方法] = useState(默认值)
  const [count, setCount] = useState(0);
  return (
    <div>
      <h1>{count}</h1>
      <button type="button" onClick={() => setCount(count + 1)}>
        点击
      </button>
    </div>
  );
}
```

# useEffect

- 给函数组件新增操作副作用的能力
- 和class组件中的componentDidMount、componentDidUpdate和componentWillUnmount具有相同的用途

### useEffect出现两次

1. React18新增特性。
2. 仅在开发模式下，且使用了严格模式（Strict Mode）下会触发。生产模式不会触发，仅执行一次。
3. 执行两次是为了模拟立即卸载组件和重新挂载组件。为了开发者提前发现重复挂载造成的Bug的代码。
4. React版本加入了分片更新，fiber架构。组件可能执行多次，目的就是重复执行useEffect的时候不会影响最终结果。

### 状态更新

1. 第一次初始化
2. 第二次更新

### return

卸载

### 执行时机

- 必须在DOM真实挂载在页面中，或者已经DOM更新完成才执行
- 多个useEffect执行时机是一样的，顺序按代码顺序

### 执行顺序

- 第一次执行，初始化
- 第二次执行，卸载上一次，再进行更新
- 第三次执行，卸载上一次，再进行更新

```js
// 第一轮
// useEffect执行 -- 初始化
//                       -- 状态更新,拿到的值是初始化的值  1
//第二轮
// return             -- 卸载,卸载的值是上一次的值   1
// useEffect执行  -- 初始化
//                        -- 状态更新，拿到当前更新的值 2
// 第三轮
// return             -- 卸载，卸载的值是上一次的值 2
// useEffect执行  -- 初始化
//                        -- 状态更新，拿到当前更新的值 3
```

### 需要监听指定的值是否更新

- ` useEffect(()=>{},[])`
- 第二个参数传入空数组 即不更新
- 传入什么值就监听什么值，该值不修改不到则不更新

# 自定义hook

- use开头一般就是自定义hooks,它是抽象通用逻辑
- 实际上就是一个通用函数，返回值

# useContext

- 接收一个React.createContext组件
- `const btnContext = useContext(ButtonContext);`

# useCallback

- 缓存函数，每次都是原来的函数，只要依赖不发生变化就不更新

```JS
class ButtonCon extends React.PureComponent {
  // shouldComponentUpdate(nextProps, nextState) {
  //   if (nextProps.onChangeClick === this.props.onChangeClick) {
  //     return false;
  //   }
  //   return true;
  // }
  render() {
    const { onChangeClick, children } = this.props;
    return (
      <p>
        <button type="button" onClick={onChangeClick}>
          {children}
        </button>
        <span>{Math.random()}</span>
      </p>
    );
  }
}

function App() {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);
  const [count3, setCount3] = useState(0);

  let handleClick1 = () => {
    setCount1(count1 + 1);
  };
  let handleClick2 = useCallback(() => {
    setCount2(count2 + 1);
  }, [count2]);
  return (
    <div>
      <ButtonCon onChangeClick={handleClick1}>点击1</ButtonCon>
      <ButtonCon onChangeClick={handleClick2}>点击2</ButtonCon>
      <ButtonCon onChangeClick={() => setCount3(count3 + 1)}>点击3</ButtonCon>
    </div>
  );
}
```

##### 函数式组件使用useCallback

- `React.memo(函数组件)`
- 只会对props进行遍历，state修改还是会更新

```js
function ButtonCon1(props) {
  const { onChangeClick, children } = props;
  return (
    <p>
      <button type="button" onClick={onChangeClick}>
        {children}
      </button>
      <span>{Math.random()}</span>
    </p>
  );
}
const ButtonCon =React.memo(ButtonCon1)
```

# useMemo

对state做缓存

```js
  const userinfo = useMemo(() => {
    return {
      name: 1,
    };
  }, []);
```

# useRef

在函数式组件中获取元素节点，元素ref使用

# Hooks重构？

- 在新的页面中尝试使用hooks
- 官网建议：我们并没有计划从React中移除class，推荐使用hook，不建议重构

# 为什么不建议老的项目使用hooks进行重构

- 生命周期没有覆盖全（getSnapshotBeforeUpdate,getDerivedStateFromError、componentDidCatch）
- 老的代码中的第三方库可能不支持hook
