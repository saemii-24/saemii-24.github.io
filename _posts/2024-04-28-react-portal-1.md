---
title: "React Portal 사용하기(1)"
author: saemii
categories:
  - React
tags:
  - [React]
date: 2024-04-28
last_modified_at: 2024-04-28
pin: true
---

## 📌시작하며

최근 강의를 수강하며 학습용 프로젝트를 진행 중이다. 강의 내용 중에 React Portal을 사용해 Modal을 만드는 부분이 나오는데, 해당 기능의 존재는 알고있었지만 실제 프로젝트에 적용해보는 건 처음라서, 강의 내용을 기반으로 스스로 학습한 내용을 추가해 정리해보고자 한다.

## ✅React Portal 이란

공식문서에서는 이 기능을 다음과 같이 설명한다.

> Portal은 부모 컴포넌트의 DOM 계층 구조 바깥에 있는 DOM 노드로 자식을 렌더링하는 최고의 방법을 제공합니다.

글로만 보면 이해하기 어려운데, 코드로 보면 이해하기 쉽다. React의 `index.html`파일을 살펴보자. 기본적으로는 다음과 같은 구조를 가지고 있다.

```html
<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
</body>
```

그리고 우리가 만드는 컴포넌트들은 `<div id="root"></div>`의 안에서 렌더링 되게 된다. 하지만 Portal을 쓰면 이 내부에서 렌더링 되는것이 아니라 외부 DOM 요소에 렌더링 할 수 있다.

예를 들어, 다음과 같은 형식을 가질 수 있다. 이렇게 하면, Modal을 만들 때 id='root'요소에 자유로워지므로 다른 요소들의 z-index나 부모 요소로부터의 스타일 영향을 받지 않아, 훨씬 편리하게 Modal을 만들 수 있다.

```html
<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
  <div id="portal"></div>
</body>
```

## ✅React Portal 사용하기

### ➡️외부 DOM요소 생성하기

먼저 `index.html`에 외부 DOM 요소를 생성해준다.

```html
<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
  <div id="portal" 👈></div>
</body>
```

### ➡️createPortal을 사용해 렌더링 하기

#### 💟createPortal이란?

`createPortal(children, domNode, key?)`
portal을 생성하려면 createPortal을 호출하면 된다. createPortal은 2가지 인자와 1가지 옵션을 갖는다.
|값|설명|
|--|--|
|children|element, component 등 React로 렌더링할 수 있는 모든 것|
|domNode|`document.getElementById()`를 통해 얻을 수 있는 DOM 노드.<br/> 즉, 해당 노드는 이미 존재해야 한다. <br/> 업데이트 중에 다른 DOM 노드를 전달하면 포털 콘텐츠가 다시 생성된다.|
|key(옵션)|포털의 키로 사용할 고유한 문자열 또는 숫자|

#### 💟사용예제

위에서 `index.html`에 외부 DOM 요소인 `<div id="portal" 👈></div>`를 생성해 두었으므로, 이것과 함께 미리 만들어 둔 Modal 컴포넌트를 가져와서 사용해보자.

```typescript
import { createPortal } from "react-dom"
import MainContent from "@component/MainContent"
import Modal from "@component/Modal"

const modalDiv = document.getElementById("portal")

export default function App() {
  return (
    <>
      <MainContent />
      {createPortal(<Modal />, modalDiv)}
    </>
  )
}
```

#### 💟Modal Open & Close

추가적으로, Modal의 열고 닫는 것을 관리하려면 다음과 같이 할 수 있다.

```typescript
import { createPortal } from "react-dom"
import MainContent from "@component/MainContent"
import Modal from "@component/Modal"

const modalDiv = document.getElementById("portal")

export default function App() {
  const [modalOpen, setModalOpen] = useState(false)

  const openModal = () => {
    setModalOpen(true)
  }

  const closeModal = () => {
    setModalOpen(false)
  }

  return (
    <>
      <MainContent />
      {modalOpen && createPortal(<Modal onClose={closeModal} />, modalDiv)}
    </>
  )
}
```

## 📩마무리

이렇게 portal을 이용해서 모달을 만드는 방법에 대해 알아봤다. 관련 내용을 검색하다가 `<dialog>` 태그를 알게 되었는데 이건 이어서 다음 포스팅에서 정리하고자한다.

## 🗂️참고 사이트

- https://ko.legacy.reactjs.org/docs/portals.html
- https://react.dev/reference/react-dom/createPortal
