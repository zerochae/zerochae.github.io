---
title: Essay &#35;3
description: Wanted PreOnBoarding FrontEnd Week 2
date: 2022-02-09 09:00
category: Essay
tags: [Think]
---

크고 작은 이슈들이 있었다. (아주 작은 이슈도)

## Second Parameter in a Component

부모 Component에서 props를 받아와서 사용하려고 하다가, 어이 없는 실수를 했다.

```jsx
// main.index.tsx

const [filter,setFilter] useState = useState<type>();
const [fetchData , setFetchData] = useState<type>();

const main = () => {

  return (
    <Filter data={fetchData} filter={filter} setFilter={setFilter} />
  )
}
```

전체를 감싸는 `Main` Component에서는 API Response Data를 받아와서 저장하는 `fetchData` 와 필터링을 하는 조건을 저장하는 `filter` 가 있다. filter state를 `Filter`가 아닌 `Main`에 만든 이유는 데이터는 위에서부터 아래로 흘러야 하기 때문에 컴포넌트를 감싸고 있는 부모에 만드는게 자연스럽다고 생각했다.

그리고 `Filter` Component에서의 `Select` Component 안에 Props를 지정해주었다.

```jsx
// Filter.index.tsx

interface FilterPropsType {
  filter: FilterType;
  setFilter: React.Dispatch<React.SetStateAction<FilterType>>;
}

const Filter = ({ filter, setFilter }: FilterPropsType) => {
  ...
  return (
    <Select data={data} filter={filter} setFilter={setFilter}/>
    <Toggle data={data} filter={filter} setFilter={setFilter}/>
  )
}

const Select = ({data}: DataType,{ filter, setFilter }: FilterPropsType) => {
  console.log(filter) // undefined
}

const Toggle = ({ filter, setFilter }: FilterPropsType) => {
  console.log(filter) // { method:[],material:[],check:false }
}
```

정말 이상하게도 똑같은 props를 전달했는데, 왜 하나는 정상적으로 나오고, 하나는 undefined이 출력되는건지 알 수 없었다. 왜 그땐 생각을 못했을까... `Select` Component의 Parameter를 2개 쓰고 있었다는걸. 부끄러운 실수를 했다..

그렇다면 이 두번째 parameter는 어디에 쓰이는 걸까. 이놈의 존재가 궁금해졌다.

일단 콘솔 로그로 출력해보면

```jsx
const App = (props, whatIsThis) => {
  console.log(whatIsThis); // {} ... 비어있는 Object가 출력된다.
};
```

역시나 나와 같은 궁금증을 가진 사람이 있었다.

[What is the second parameter for in a React functional component?](https://stackoverflow.com/questions/56879095/what-is-the-second-parameter-for-in-a-react-functional-component)

위의 글을 찾아본 결과 두번째 파라미터의 쓰임은 두가지이다.

1. Context API

함수형 컴포넌트에서 context에 정의된 정보를 가진 객체를 두번째 Parameter로 전달 받을 수 있다.

```jsx
import PropTypes from 'prop-types';

const ContextConsumer = (props, context) => {
  console.log(context); // { name: "zerochae" }
  return …;
};

ContextConsumer.contextTypes = { // 안써주면 에러나요
  name: PropTypes.string,
  …
};

export default ContextConsumer;
```

`zerochae`를 Provider에서 정의해준다.

Provider는 이렇게 생겼다.

```jsx
import { Component } from "react";
import PropTypes from "prop-types";

class ContextProvider extends Component {
  constructor(props) {
    super(props);
    this.state = { name: "zerochae" };
  }
  getChildContext() {
    return {
      name: this.state.name,
    };
  }
  render() {
    return this.props.children;
  }
}

ContextProvider.childContextTypes = {
  name: PropTypes.string,
};

export default ContextProvider;
```

이걸 함수형 컴포넌트로 바꿔보려고 하니..

```js
Warning: ContextProvider(...): 
childContextTypes cannot be defined on a function component.at ContextProvider
```

음.. 이런 에러가 발생했다. 그리고 이제 더 이상 사용하지 않아야 한다고 답변이 있었다.

2. Ref

두번째는 DOM 조작에 관련되어있다. 두번째 매개변수에 ref를 정의하여 전달할 수 있다.

```js
const App = () => {

  console.log(ref.current); // <button>이건데요</button>

  return (
    <div className="App">
      <MyButton ref={ref}>이거예요?</MyButton>
    </div>
  );
};

const ref = React.createRef();
const MyButton = React.forwardRef((props, ref) => (
  <button ref={ref}>이건데요</button>
));
```

## Story Book

작성중..

## Jest

작성중..