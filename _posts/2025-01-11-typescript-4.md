---
title: "Typescript(4) - 각 Element의 Props 속성을 정해보자"
author: saemii
categories:
  - Typescript
tags:
  - [Typescript]
date: 2024-01-11
last_modified_at: 2024-01-11
pin: true
---

## 📌시작하며

컴포넌트를 만들다 보면, `onClick`이나 `onMouseOver`등의 이벤트를 props로 받아와야 하는 일이 간혹 생기곤 한다. 물론 props로 내려줘서 작업해도 문제는 없지만, 이벤트가 하나 더 발생하면, 컴포넌트에 정의해둔 props의 interface안에 작성해줘야 하는 번거로움이 생긴다.

그래서 미리 정의할 때 타입을 확장해서 해당 HTML 요소에 알맞은 props가 내려오는 것을 명시해줄 수 있다.

실제로 내가 자주 사용하고 있는 방식이라 문서로 정리해두고자 한다.

## ✅Button Component

버튼 컴포넌트를 만들어보자.
나는 평소 className과 children을 내려주는 방식을 자주 사용해서 이 부분도 함께 포함해서 작성한다.

```typescript
import React from "react"
import cn from "@utils"

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  className?: string
}

const Button = ({ className, children, ...props }: ButtonProps) => {
  return (
    <button className={cn("my-class", className)} {...props}>
      {children}
    </button>
  )
}

export default Button
```

## ✅타입 스크립트 해석하기

`ButtonProps`란 이름의 interface를 작성한다. 이때 ButtonProps는 `React.ButtonHTMLAttributes<HTMLButtonElement>`값으로 한정한다.

그럼 각각 `React.ButtonHTMLAttributes`와 `HTMLButtonElement`는 무슨 타입일까?

하나씩 타고 들어가서 살펴보자.

### ➡️React.ButtonHTMLAttributes

맨 앞에 붙어있는 `React`타입은 리액트에서 사용할 JSX의 타입 정의이다.

```typescript
declare module "react" {
  interface StyleHTMLAttributes<T> extends HTMLAttributes<T> {
    jsx?: boolean
    global?: boolean
  }
}
```

즉 `React`에서 사용할 JSX 타입 중에서도 `ButtonHTMLAttribues` 타입을 사용하겠다는 의미가 된다.
이때 `ButtonHTMLAttribues`은 이름 그대로, `button`태그에서 사용하는 속성들을 포함한다.

```typescript
interface ButtonHTMLAttributes<T> extends HTMLAttributes<T> {
  disabled?: boolean | undefined
  form?: string | undefined
  formAction?:
    | string
    | DO_NOT_USE_OR_YOU_WILL_BE_FIRED_EXPERIMENTAL_FORM_ACTIONS[keyof DO_NOT_USE_OR_YOU_WILL_BE_FIRED_EXPERIMENTAL_FORM_ACTIONS]
    | undefined
  formEncType?: string | undefined
  formMethod?: string | undefined
  formNoValidate?: boolean | undefined
  formTarget?: string | undefined
  name?: string | undefined
  type?: "submit" | "reset" | "button" | undefined
  value?: string | readonly string[] | number | undefined
}
```

### ➡️HTMLButtonElement

`HTMLButtonElement`는 button 요소의 동작을 제어하고 조작하는 속성과 메서드를 포함한다. 즉, `React.ButtonHTMLAttributes`가 리액트에서 사용할 button의 jsx **props** 를 정의한다면, `HTMLButtonElement`는 실제 button 태그의 DOM 요소를 조작하는 **속성과 메서드**를 정의하고 있는 것이다.

이 두가지를 같이 사용해 button 태그에 관련된 props와 DOM 조작을 정해진 타입 안에서 안전하게 할 수 있다.

```typescript
interface HTMLButtonElement extends HTMLElement, PopoverInvokerElement {
  disabled: boolean
  readonly form: HTMLFormElement | null
  formAction: string
  formEnctype: string
  formMethod: string
  formNoValidate: boolean
  formTarget: string
  readonly labels: NodeListOf<HTMLLabelElement>
  name: string
  type: "submit" | "reset" | "button"
  readonly validationMessage: string
  readonly validity: ValidityState
  value: string
  readonly willValidate: boolean
  checkValidity(): boolean
  reportValidity(): boolean
  setCustomValidity(error: string): void
  addEventListener<K extends keyof HTMLElementEventMap>(
    type: K,
    listener: (this: HTMLButtonElement, ev: HTMLElementEventMap[K]) => any,
    options?: boolean | AddEventListenerOptions
  ): void
  addEventListener(
    type: string,
    listener: EventListenerOrEventListenerObject,
    options?: boolean | AddEventListenerOptions
  ): void
  removeEventListener<K extends keyof HTMLElementEventMap>(
    type: K,
    listener: (this: HTMLButtonElement, ev: HTMLElementEventMap[K]) => any,
    options?: boolean | EventListenerOptions
  ): void
  removeEventListener(
    type: string,
    listener: EventListenerOrEventListenerObject,
    options?: boolean | EventListenerOptions
  ): void
}
```

## ✅응용하기

이 기능을 이용해 다른 `html` 태그도 얼마든지 만들어 낼 수 있다.

```typescript
import React from "react"
import cn from "@utils"

interface DivProps extends React.DivHTMLAttributes<HTMLDivElement> {
  className?: string
}

const Div = ({ className, children, ...props }: DivProps) => {
  return (
    <div className={cn("my-class", className)} {...props}>
      {children}
    </div>
  )
}

export default Div
```

이 방식을 사용하면 내가 만든 컴포넌트들에 `onClick` 같은 메소드를 props로 받겠다고 하나하나 작성하지 않아도 된다.

### ➡️ 기존 방식을 사용할 때

```typescript
import React from "react"
import cn from "@utils"

const Parent = () => {
  const [setOpen, setIsOpen] = useState<boolean>(false)

  return (
    <div>
      <Div setIsOpen={setIsOpen}>클릭하면 닫힌다.</Div>
    </div>
  )
}

interface DivProps {
  className?: string
  setOpen: () => boolean
}

const Div = ({ className, children, setOpen }: DivProps) => {
  return (
    <div
      className={cn("my-class", className)}
      onClick={() => {
        setOpen(false)
      }}
    >
      {children}
    </div>
  )
}

export default Div
```

### ➡️ 타입을 한정할 떄

```typescript
import React, { useState } from "react"
import cn from "@utils"

const Parent = () => {
  const [setIsOpen, setOpen] = useState<boolean>(false)

  return (
    <div>
      <Div onClick={() => setOpen(false)}>클릭하면 닫힌다.</Div>
    </div>
  )
}

interface DivProps extends React.HTMLProps<HTMLDivElement> {
  className?: string
}

const Div = ({ className, children, ...props }: DivProps) => {
  return (
    <div className={cn("my-class", className)} {...props}>
      {children}
    </div>
  )
}

export default Div
```

## 🗂️참고 사이트

- <https://developer.mozilla.org/en-US/docs/Web/API/HTMLButtonElement/type>
