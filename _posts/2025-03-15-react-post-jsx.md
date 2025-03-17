---
title: "[리액트] React JSX"
excerpt: "React JSX"

categories:
  - java
tags:
  - [tag1, tag2]

permalink: /java/post-name-here-4/

toc: true
toc_sticky: true

date: 2025-03-15
last_modified_at: 2025-03-15
---

## 🦥 본문

# React JSX?

JSX는 자바스크립트 확장 문법이며 실행되기 전 코드가 번들링 되는 과정에서 바벨을 사용하여 일반 자바스크립트 형태의 코드로 변환됩니다.

다음은 JSX  코드의 예시 입니다.

```jsx
function App(){
	return (
		<div>
			Hello <b>react</b>
		</div>
	)
}"_)_
```

위 코드는 바벨에 의해 다음과 같이 변환 됩니다.

```jsx
function App() {
	return React.createElement("div", null, "Hello", React.createElement("b", null, "react));
}
```

---

JSX 에서는 주의해야할 문법 사항이 몇가지 있습니다.

컴포넌트는 반드시 부모 요소 하나로 감싸져야 합니다.

```jsx
function App() {
  return (
    <h1>H1 태그</h1>    
    <h2>H2 태그</h2>
  );
}
```

이 경우 부모 요소 하나로 감싸져 있지 않아 다음과 같은 오류가 발생합니다.

```jsx
   5 |   return (
   6 |     <h1>H1 태그</h1>    
>  7 |     <h2>H2 태그</h2>
     |     ^
   8 |   );
   9 | }
  10 |
ERROR in ./src/App.js
Module build failed (from ./node_modules/babel-loader/lib/index.js):
SyntaxError: C:\lee\react\hello-react\src\App.js: Adjacent JSX elements must be wrapped in an enclosing tag. Did you want a JSX fragment <>...</>? (7:4)
```

React 에서는 컴포넌트의 변화를 감지할 때 효율적으로 비교할 수 있도록 컴포넌트 내부는 하나의 DOM Tree 구조로 이루어져야 한다는 규칙이 있습니다.

리액트 V16 이상에서는 div 같은 특정 tag 가 아닌 fragment라는 기능을 사용해 자식 요소를 Rapping 할 수 있습니다.

```jsx
import {fragment} from 'react'

function App() {
  return (
    <fragment>
      <h1>H1 태그</h1>    
      <h2>H2 태그</h2>
    </fragment>
    
  );
}

export default App;
```

또한 자바스크립트 표현식을 사용해 변수를 Mapping 할 수 있습니다.

```jsx
function App() {
  const name = "리액트";
  return (
    <fragment>
      <h1>H1 태그</h1>    
      <h2>H2 태그</h2>
      <h3>{name} 안녕</h3>
    </fragment>
    
  );
}

export default App;

```

JSX 내부에서는 if문을 사용할 수 없습니다, 조건에 따라 사용에야 한다면 JSX 밖에서 if문을 사용하거나 삼항 연산자를 사용해야 합니다.

```jsx
function App() {
  const name = "리액트";
  return (
    <div>
      {name == '리액트' ? (
        <h1>이름은 리액트 입니다</h1>
      ) : (
        <h2>리액트가 아닙니다</h2>
      )}
    </div>
    
  );
}
```

조건부 연산자를 통해 렌더링 여부를 결정할 수 있습니다.

```jsx
function App() {
  const name = "리액트";
  return (
    <div>{name === '리액트' ? <h1>리액트 입니다.</h1> : null}</div>    
  );
}

export default App;
```

```jsx
function App() {
  const name = "리액트";
  return (
    <div>{name === '리액트' && <h1>리액트 입니다.</h1>}</div>    
  );
}
```

이때 숫자 0은 예외적으로 화면에 나타납니다.

```jsx
function App() {
  const number = 0;
  return number && <div>내용</div>
}
```

컴포넌트는 undefine를 반환하여 렌더링하는 상황을 만들면 오류가 발생합니다, 만약 값이 undefined일 수 도 있다면 OR 연산자를 사용해 값을 지정하여 오류를 방지하여야 합니다, 반면 JSX 내부에서 undefined 를 렌더링하는것은 괜찮습니다.

```jsx
function App() {
  const name = undefined;
  return name || "값이 undefined 입니다.";
}
```

```jsx
function App() {
  const name = undefined;
  return (
    <div>{name || '리액트'}</div>
  );
}

export default App;
```

React 에서는 DOM 요소에 스타일을 적용할 때는 문자열 형태로 넣는 것이 아닌 객체 형태로 넣어야 합니다, backgroud-color 의 경우 카멜표기법으로 backgroudColor로 작성해야 합니다.

```jsx
function App() {
  const name = '리액트';
  const style = {
    backgroundColor : 'black'
  };
  return (
    <div style={style}>{name}</div>
  );
}
```

★ JSX 에서는 CSS 클래스를 사용할 때  class 가 아닌 ClassName을 사용해야 합니다.

```css
.react {
  background: aqua;
  color: black;
  font-size: 47px;
  font-weight: bold;
  padding: 16px;
}
```

```jsx
import './App.css';

function App() {
  const name = '리액트';
  const style = {
    backgroundColor : 'black'
  };
  return (
    <div className="react">{name}</div>
  );
}
```