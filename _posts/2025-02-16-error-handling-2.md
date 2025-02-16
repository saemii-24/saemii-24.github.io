---

title: "에러 핸들링 - error.tsx"
author: saemii
categories: 
tags: - [Next.js]
date: 2025-02-16
last_modified_at: 2025-02-16
pin: true

---

## 📌시작하며

전 포스팅에서는 `global-error.tsx`로 최상위 layout.tsx에 에러가 발생했을 때 이를 감지하고, 적절한 UI를 렌더링 하는 방식에 대해 살펴보았다. 이번에는 각 페이지에 사용되는 `error.tsx`에 대해 알아보자.

이전부터 `error.tsx`의 존재를 알고 있었고, 종종 프로젝트에 사용도 했었는데 어떤 순간에서는 적절히 렌더링이 되다가도,
특정 에러에서는 좌측 하단에 빨간 에러 창만 뜨고, `error.tsx`가 렌더링이 안되는 경우가 있었다.😑

그 이유도 함께 정리하고자 한다.

## ✅시도한 error

일단 `error.tsx`를 실행시키려면, error를 만들어줘야 하기 때문에 page.tsx에 다음과 같이 작성했다.
button을 클릭하면 `throwError`함수가 실행되어 에러를 발생시키고, 이 떄문에 만들어 둔 error.tsx가 제대로 작동할 거라 생각헀다.
그런데... 이 코드는 `error.tsx`가 렌더링 되지 않는다! 😥

```javascript
"use client"

import Container from "@/components/Container"

export default function ErrorExample() {
  function throwError() {
    throw new Error("에러 강제 발생!")
  }

  return (
    <Container>
      <Container.Title>에러 핸들링</Container.Title>
      <button
        type="button"
        onClick={throwError}
        className="w-full text-center bg-red-500 text-white h-8 rounded-md mt-3 cursor-pointer hover:bg-red-500/70"
      >
        에러 발생
      </button>
    </Container>
  )
}
```

## ✅다시 시도한 error

이유를 알 수 없어서 유튜브에서 해당 개념을 설명하는 분의 예제를 보고 다시 시도했더니 이번에는 정상적으로 만들어 둔 `error.tsx`가 렌더링 되었다.

```javascript
function getRandom(count: number) {
  return Math.floor(Math.random() * count)
}

export default function testPage() {
  const random = getRandom(2)

  if (random === 1) {
    throw new Error("에러 발생!")
  }

  return <div>에러를 테스트합니다.</div>
}
```

## ❓왜 차이가 발생할까

그렇다면 왜 어떤 상황에서는 `error.tsx`가 제대로 렌더링 되고, 또 다른 상황에서는 렌더링 되지 않을까?

처음 생각했던 이유는 내가 만든 예제는 `'use client'`가 쓰여진 클라이언트 컴포넌트이고, 유튜브 예제는 서버 컴포넌트기 떄문이지 않을까 였다.🤨

하지만 `error.tsx`도 클라이언트 측에 작성되고, 실습을 하기전에 살펴봤던 `error.tsx`예제들이 전부 서버 컴포넌트였던 것은 아니었기 때문에 이 문제는 아닐 것이었다.

좀 더 찾아보니 이유를 알 수 있었는데, next.js에서 `error`를 감지하고, `error.tsx`를 렌더링 하는 방식에 힌트가 있었다.

`error.tsx`는 page.tsx를 감싸는 React의 Error Boundary를 자동으로 생성하는 역할을 한다. React 공식문서에서는

> 에러 경계는 하위 컴포넌트 트리의 어디에서든 자바스크립트 에러를 기록하며 깨진 컴포넌트 트리 대신 폴백 UI를 보여주는 React 컴포넌트입니다. 에러 경계는 렌더링 도중 생명주기 메서드 및 그 아래에 있는 전체 트리에서 에러를 잡아냅니다.

라고 설명하는데, 여기서 집중할 내용은 **렌더링 도중 생명주기 메서드 및 ~** 부분이다.
생명주기 메서드라면, 컴포넌트의 생성부터 소멸까지의 과정 **(마운트, 언마운트, 업데이트)** 를 말하며, 함수형 컴포넌트에서는 useEffect훅을 사용해 구현할 수 있는 부분이다.

즉 내가 만든 예시는 에러 발생이 단순히 버튼을 누르는 `eventListener`에 의해 실행 된 것으로, 생명주기에 포함되지 않기 때문에 next.js에서 에러를 감지하지 못해 `error.tsx`를 렌더링 하지 못한 것이다!

반대로, 유튜브 예제에서는 마운트 될 때 함수가 자동 실행되며, 특정상황에 에러가 발생되기 떄문에 next.js에서 에러를 감지할 수 있었던 것이다.

그럼 어떻게 내 예제를 바꿔야 제대로 렌더링이 발생할까?

## ✅error.tsx를 정상 작동하게 만드는 예제

이번에는 똑같은 클라이언트 측 컴포넌트지만, 단순히 버튼을 눌렀을 때 `throwError` 함수를 작동시키는 것이 아니라 useState와 useEffect를 사용해 관리하게 하였다.

```typescript
"use client"
import { useEffect, useState } from "react"
import Container from "@/components/Container"

export default function ErrorExample() {
  const [error, setError] = useState<Error | null>(null)

  function throwError() {
    setError(new Error("에러 강제 발생!!"))
  }

  //next.js는 렌더링 과정에서 발생한 에러만 감지한다.
  useEffect(() => {
    if (error) throw error
  }, [error])

  return (
    <Container>
      <Container.Title>에러 핸들링</Container.Title>
      <button
        type="button"
        onClick={throwError}
        className="w-full text-center bg-red-500 text-white h-8 rounded-md mt-3 cursor-pointer hover:bg-red-500/70"
      >
        에러 발생
      </button>
    </Container>
  )
}
```

이제 버튼을 누르면 미리 만들어 둔 error.tsx가 제대로 작동한다!

```javascript
"use client"

export default function ErroBoundary({
  error,
  reset,
}: {
  error: Error,
  reset: () => void,
}) {
  return (
    <div>
      <h2>에러가 발생했습니다.</h2>

      <button
        className="h-10 px-10 rounded-lg bg-red-500 text-white cursor-pointer"
        onClick={() => reset()}
      >
        재시도
      </button>
    </div>
  )
}
```

## 🗂️참고 사이트

- <https://www.youtube.com/watch?v=UcnCZ0lXZbo&ab_channel=Codevolution>
- <https://ko.legacy.reactjs.org/docs/error-boundaries.html>
- <https://nextjs.org/docs/app/building-your-application/routing/error-handling>
