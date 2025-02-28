---
title: "resizable 컴포넌트를 만들어보자!"
author: saemii
categories:
  - Nextjs
tags:
  - [Nextjs]
date: 2025-02-28
last_modified_at: 2025-02-28
pin: true
---

## 📌시작하며

최근 제작하고 있는 웹이 한 번에 표시되어야 하는 정보가 많아, 좌측엔 내비게이션을 두고, 우측엔 해당 페이지에 필요한 정보를 두었다.🤔

좌측 내비게이션은 사이즈가 그리 크지 않고, 펼치거나 접을 수 있기 때문에 괜찮지만, 사용자가 가운데 내용을 보면서 정보 컴포넌트를 함께 보고 싶은 경우에는 고정 사이즈를 제공하는 것보다, 사용자의 화면 크기에 따라 직접 정보 컴포넌트를 줄일 수 있었으면 더 좋은 사용자 경험을 만들 수 있을거란 생각이 들었다.

그래서 이번에는 resizable 한 컴포넌트를 만드는 방법을 정리해보고자 한다!

## 💻커서 종류

사용자가 해당 컴포넌트의 사이즈 조절이 가능하다는 것을 명확히 알기 위해, `<->` 모양의 커서를 사용해야 한다. 먼저 mdn 사이트에서 커서 종류를 살펴보았고, `col-resize`, `row-resize`, `ns-resize` 그리고 `ew-resize`의 형태를 이용하기로 했다.

tailwind에서도 간단하게 class명으로 커서의 형태를 정해주는데, 정말 다양한 종류가 있지만 간단하게 정리해보자면 다음과 같다.

| Tailwind 클래스     | CSS 속성     | 형태 |
| ------------------- | ------------ | ---- |
| `cursor-col-resize` | `col-resize` | ↕️   |
| `cursor-row-resize` | `row-resize` | ↔️   |
| `cursor-ew-resize`  | `ew-resize`  | ↕️   |
| `cursor-ns-resize`  | `ns-resize`  | ↔️   |

참고로, `ew` 나 `ns`는 방향을 나타낸다.

- n → North (위쪽)
- s → South (아래쪽)
- e → East (오른쪽)
- w → West (왼쪽)

## 🖱️마우스 이벤트

resizable 컴포넌트를 만들 때 사용되는 마우스 이벤트도 알아보자.

### 📌Mousemove

`mousemove` 이벤트는 마우스 커서가 evnetListener를 가지고 있는 해당 요소 내에서 움직일때 발생한다.

### 📌MouseDown

`mousedown` 이벤트는 마우스 혹은 트랙버튼의 버튼이 눌렸을 때 발생한다.

### 📌MouseUp

`mouseup` 이벤트는 마우스 혹은 트랙버튼을 누르고 있던 버튼이 놓였을 때 발생한다.

## 🧩clientX와 ClientY

스크롤이나 화면에서 일어나는 이벤트에서 자주 접하게되는 속성인 `clientX`와 `clientY`에 대해 알아보자!

`MouseEvent.clientX`와 `MouseEvent.clientY`는 뷰포트 내의 마우스의 위치를 알려준다.

- `clientX` : 뷰포트 내의 수평 좌표
- `clientY` : 뷰포트 내의 수직 좌표

⭐주의할 점은, 마우스 위치의 기준이 컴포넌트 (페이지 길이)가 아니라, **뷰포트** 라는 것!
즉 페이지가 얼마나 긴지에 상관없이, 뷰포트 좌측 하단은 항상 `clientX`가 0이라는 사실이다.

## 👨🏻‍💻예제

먼저 사이즈가 조절 되어야 하는 자식 요소 두 가지를 만들어 주었다. 사용하고 있는 Container가 모바일 정도의 사이즈라, 좌우가 아닌 **상하** 로 조절 될 수 있도록 세팅해주었다.

먼저 구분하기 좋게 Top 컴포넌트와 Bottom 컴포넌트를 준비했다.

```typescript
interface DivProps extends HTMLAttributes<HTMLDivElement> {
  className?: string
}

const Top = ({ className, ...props }: DivProps) => {
  return (
    <div className={cn("w-full h-full bg-blue-400", className)} {...props}>
      top
    </div>
  )
}

const Bottom = ({ className, ...props }: DivProps) => {
  return (
    <div className={cn("w-full bg-pink-400", className)} {...props}>
      Bottom
    </div>
  )
}
```

이제 부모 요소를 만들어 준다.
사용하고 있는 `Container` 컴포넌트 안에 Top과 Bottom을 위치시켜주었다.

이때 div로 Top과 Bottom을 구분하기 위한 라인을 만들어 주었고, `absolute`로 `Top`컴포넌트 바닥에 딱 맞게 붙여 주었다.

커서는 위에서 살펴본 상하 화살표를 나타내는 `cursro-ns-resize`를 이용해 마우스나 트랙패드 커서를 해당 라인 가까이에 갔을 때, 사용자에게 해당 컴포넌트의 사이즈를 조절 할 수 있음을 시각적으로 표시하였다.

```typescript
"use client"
import React, { HTMLAttributes, useState, useRef } from "react"
import Container from "@/components/Container"
import { cn } from "utils/cn"

const Resizable = () => {
  /*생략*/
  return (
    <Container>
      <Container.Title>Resizable 컴포넌트</Container.Title>
      <div
        ref={containerRef}
        className="h-[calc(100dvh-150px-12px)] mt-3 w-full bg-red-50"
      >
        <div style={{ height: `${topHeight}px` }} className="relative w-full">
          <Top />
          // Top과 Bottom을 구분하기 위한 라인
          <div
            onMouseDown={handleMouseDown}
            className="w-full h-1 absolute bottom-0 left-0 right-0 bg-black cursor-ns-resize"
          />
        </div>
        <Bottom style={{ height: `calc(100% - ${topHeight}px)` }} />
      </div>
    </Container>
  )
}

export default Resizable
```

변수 선언부를 살펴보자.

- 기본적으로 `Top` 컴포넌트의 height 값을 300으로 지정해주었다.
- `isResizing`을 통해 사용자가 현재 리사이징을 시도하는지 확인한다.
- `containerRef`를 통해 resize 이벤트가 일어나는 구간을 확인하고 제한한다.
- `startY`와 `startHeight`를 통해 마우스 이벤트에서 일어나는 컴포넌트 크기 조절과 사용자 마우스 위치의 오차를 조정한다.

```typescript
const [topHeight, setTopHeight] = useState(300)
const isResizing = useRef(false)
const containerRef = useRef<HTMLDivElement | null>(null)
const startY = useRef(0) // 마우스 클릭 시점의 Y 좌표
const startHeight = useRef(0) // 마우스 클릭 시점의 Top 높이
```

이벤트를 살펴보자

```typescript
const handleMouseMove = (e: MouseEvent) => {
  if (!isResizing.current || !containerRef.current) return

  // 마우스가 이동한 거리
  const deltaY = e.clientY - startY.current
  let newHeight = startHeight.current + deltaY

  if (newHeight < 200) newHeight = 200
  if (newHeight > 400) newHeight = 400

  setTopHeight(newHeight)
}
```

- 마우스가 움직일 때마다 호출되어 크기를 조정한다.
- 마우스를 누른 시점의 Y좌표와 뷰포인트 내의 마우스 차이를 계산해 마우스 이동 거리를 구하고,<br/> 이전값에 더해 새로운 높이를 계산한다.
- `Height`는 200~400의 값을 가질 수 있다. (그 사이에서 리사이징 됨)

```typescript
const handleMouseDown = (e: React.MouseEvent) => {
  e.preventDefault()
  isResizing.current = true

  // 현재 마우스 위치와 Top의 현재 높이
  startY.current = e.clientY
  startHeight.current = topHeight

  document.addEventListener("mousemove", handleMouseMove)
  document.addEventListener("mouseup", handleMouseUp)
}
```

- 사용자가 마우스를 누르고 있을 때 작동한다.
- 드래그 되는 등의 기본 동작을 방지한다.
- 마우스 클릭 시점의 좌표와 현재 높이를 저장한다.

```typescript
const handleMouseUp = () => {
  isResizing.current = false
  document.removeEventListener("mousemove", handleMouseMove)
  document.removeEventListener("mouseup", handleMouseUp)
}
```

- `mouseup`상황에서는 리사이징을 끝내고. 이벤트 리스너를 제거한다.

## ✅마무리!

이 과정을 통해 상하로 리사이징 되는 컴포넌트를 만들 수 있었다!
시작하기 전에는 엄청 복잡한 코드일 거라 생각했는데, 생각보다 코드 양이 많지 않았고 더 쉽게 구현할 수 있어 앞으로 필요할 때 적절히 활용하면 좋을 것 같다.

실제 프로젝트에 적용할 수 있을지는 다른 팀원분들과 이야기해봐야겠지만, 도입하지 않더라도, 이렇게 미리 실험해보면서 기능들을 구현해보는 과정이 재밌고 뿌듯하다.😎

## 🗂️참고 사이트

- <https://tailwindcss.com/docs/cursor>
- <https://www.youtube.com/watch?v=44F_k9xJ_Lw&t=3s>
- <https://developer.mozilla.org/en-US/docs/Web/CSS/cursor>
- <https://developer.mozilla.org/ko/docs/Web/API/MouseEvent/clientX>
- <https://developer.mozilla.org/en-US/docs/Web/API/Element/mousemove_event>
