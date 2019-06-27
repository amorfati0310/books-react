### React

UI - render(state)

1. 저자도 꼽은 이유
   DOM - data binding되서 자동 render

그리고 v-dom ui 변화 최소집합을 메모리에 저장해서 빠르게 업데이트 ->불필요한 업데이트, 성능은 좋아진다.

### Question01 -> v-dom을 어떻게 구현했을까???

특징

- 렌더함수는 순수함수로 작성해야 한다.
- 컴포넌트 상태 값은 불변 객체로 관리 해야 한다.

### Question02

순수함수와 불변객체 사용 -> 함수형 지향 -> 안정성  
테스트 에도 용이

- 순수함수 및 Immutable객체를 쓰는 이유 -> 리액트에서 이런 방식을 택한 이유 겠지요, 자세히 알아보기
  저자 -> 복잡도가 낮아지고 디버깅에 용이
  -> 더 중요한 것은 이러한 요인 덕분에 렌더링 성능을 크게 향상 시킬 수 있다 !???

* 이 부분을 4장 공부하면 알 수 있다고 함 !

### 1.2 리액트 개발 환경 직접 구축하기

웹 어플리케이션 만들기 위해서

- 테스트 시스템
- 빌드 시스템
- 라우팅 시스템
  추가적
- 상태관리
- UI
  등등 신경 쓸 부분이 많다.

CRA에서 시작하면 세팅에 대한 수고를 상당히 덜어준다.

공부차원에서 toolchain? 을 이해하기 위해 직접 구축해보는 시간을 가져본다고 한다

### 1.2.1 Hello World

모든 코딩의 시작 `Hello World`

```js
// react, react-native core
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
// 웹
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>

심자 고고

react vue랑 차별화 되는 부분 중 하나 가
react를 공부하면
react-native 앱 개발에도 넘어가기 상대적으로 진입장벽이 그냥 native보다는 많이 수월한 편이다 !!!
물론 요.대.세는 flutter인 것 같다만

책에 나온 내용을 살펴보면
JScore (Webkit에 내장된 엔진)을 통해서 C++코드를 실행할 방법을 제공한다고 한다.
v-dom역시 웹 dom에 국한되지 않고 ui변화에 변경된 부분을 빨리 처리하기 위한 범용적인 기능응 한다.

```

책에서는 production도 설치하라고 하는데 아직 왜 깔아야 할지 잘 모르겠어서 dev만 고

```
React
React는 아직까진 Class 구조를 띄고 있다 점점 -> 걷어내는 방향으로 갈 것이라고 한다.
정확한 이유는 모르겠다.
// TODO
또 대충 듣기로는 Class를 쓸 이유가 없다고  -> constructor나 아무래도 엮여져 있는 methods들이 많을 텐데
굳이 연결이 없어도 되는 컴포넌트들이 많기 때문에 ... 또 있나 찾아봐서 보완하기

아무튼 요 Class는 이러한 구조로 이루어져 잇다. 

몇몇 API는 친숙하고, 몇 개는 저런것도 있네  (suspense, lazy, memo)

createContext{Children: {…}, createRef: ƒ, Component: ƒ, PureComponent: ƒ, createContext: ƒ, …}
Children: {map: ƒ, forEach: ƒ, count: ƒ, toArray: ƒ, only: ƒ}
Component: ƒ Component(props, context, updater)
Fragment: Symbol(react.fragment)
PureComponent: ƒ PureComponent(props, context, updater)
StrictMode: Symbol(react.strict_mode)
Suspense: Symbol(react.suspense)
cloneElement: ƒ cloneElementWithValidation(element, props, children)
createContext: ƒ createContext(defaultValue, calculateChangedBits)
createElement: ƒ createElementWithValidation(type, props, children)
createFactory: ƒ createFactoryWithValidation(type)
createRef: ƒ createRef()
forwardRef: ƒ forwardRef(render)
isValidElement: ƒ isValidElement(object)
lazy: ƒ lazy(ctor)
memo: ƒ memo(type, compare)
unstable_ConcurrentMode: Symbol(react.concurrent_mode)
unstable_Profiler: Symbol(react.profiler)
useCallback: ƒ useCallback(callback, inputs)
useContext: ƒ useContext(Context, unstable_observedBits)
useDebugValue: ƒ useDebugValue(value, formatterFn)
useEffect: ƒ useEffect(create, inputs)
useImperativeHandle: ƒ useImperativeHandle(ref, create, inputs)
useLayoutEffect: ƒ useLayoutEffect(create, inputs)
useMemo: ƒ useMemo(create, inputs)
useReducer: ƒ useReducer(reducer, initialArg, init)
useRef: ƒ useRef(initialValue)
useState: ƒ useState(initialState)
version: "16.8.6"
__SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED: {ReactCurrentDispatcher: {…}, ReactCurrentOwner: {…}, assign: ƒ, Scheduler: {…}, SchedulerTracing: {…}, …}
__proto__: Object

// TODO2 
__defineGetter__: ƒ __defineGetter__()
__defineSetter__: ƒ __defineSetter__()
__lookupGetter__: ƒ __lookupGetter__()
__lookupSetter__: ƒ __lookupSetter__()
왠지 요런걸 통해서 data 변화를 최적화해서 render하는애일 것 같은데 아닐까 ? 알아보기  

```


### createElement

제일 중요한 것이 createElement 만들고 렌더하는 
근데 보통은 이런 부분을 

우리가 dom에서 createElement 하고 내부에 appendchild하고 하고 attribute들 일일이 메소드로 넣어주지 않고 
최근에 template literal로 한 꺼번에 넣어주듯이 
보통 jsx라는 react용 template 문법을 통해서 한번에 render할 template을 정의해준다.

그래도 기본적인 부분을 살펴보자 

```react

React.createElement(component, props, ...children) 

``` 

고로치만 jsx를 쓰려면 babel에 도움을 받아야 한다. 

### 바벨 for jsx 

요 파트는 앞서 말했듯이 render함수 createElement하고 속성 정의해주는 부분이 매우 하드했는데 이러한 부분을 깔끔하게 작성할 수 있게 해준다.

```react

  return React.createElement(
      'div',
      null,
      React.createElement(LikeButton),
      React.createElement(
        'div',
        { style: { marginTop: 20 } },
        React.createElement('span', null, '현재 카운트: '),
        React.createElement('span', null, this.state.count),
        React.createElement(
          'button',
          { onClick: () => this.setState({ count: this.state.count + 1 }) },
          '증가',
        ),
        React.createElement(
          'button',
          { onClick: () => this.setState({ count: this.state.count - 1 }) },
          '감소',
        ),
      ),
    );
  }
  
  
 npm i @babel/core @babel/cli  @babel/preset-react 
 
 
 preset -> plugin 집합소
  ex) babel/presset-react babel-react plugin집합소
```
