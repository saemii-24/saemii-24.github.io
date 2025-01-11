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

`ButtonProps`란 이름의 interface를 작성한다. 이때 ButtonProps는 `React.ButtonHTMLAttributes`값으로 한정한다.

`React.ButtonHTMLAttributes`는 `<button>` 요소가 가질 수 있는 모든 속성(예: onClick, type, disabled 등)을 포함하고 있다.
