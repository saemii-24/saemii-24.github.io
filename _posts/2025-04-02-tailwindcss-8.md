---
title: "TailwindCSS (7) - Tailwind v3과 Container Query"
author: saemii
categories:
  - TailwindCSS
tags:
  - [Next.js, TailwindCSS]
date: 2025-04-02
last_modified_at: 2025-04-02
pin: true
---

# 🔑 들어가며

이번 포스팅은 업무 진행 중 마주쳤던 문제와 그 해결 과정에 대해 작성해보고자 한다.

# ✨구현 요구사항

화면을 구성하는 **가로로 나열된** 3개의 컴포넌트가 있다. 각각의 컴포넌트가 A, B, C 라고 할 때 A는 완전히 접을 수 있고, C는 사용자가 드래그해서 사이즈를 조절할 수 있는 resizable Component다.

마지막으로 B는 `flex-1` 로 남은 공간을 전부 차지하기 때문에, 사용자가 A와 C를 조절함에 따라 자동으로 B의 사이즈가 조정된다.

다음과 같은 상황에 A와 C는 모두 CSS 작업이 끝났지만 B는 기존에 작업했던 Media Query로는 부족하다는 아쉬움이 있었다. _ex) 사이즈를 줄였을 때 글자 깨짐 발생 등_

이 것을 해결하는 (아마) 가장 좋은 방법은 Container Query를 사용하는 것이다! 란 아이디어는 바로 떠올랐는데,

_문제는…🤔_

# ❓생각해 볼 점

현재 프로젝트에서 사용하고 있는 Tailwind CSS는 3버전으로 해당 버전 Tailwind 자체로는 Container Query를 제공하지 않고 v4에서 정식으로 들어온 것으로 알고 있었기에, 직접 CSS로 작성해야 하나? 생각하면서 먼저 관련 내용을 검색해보니 라이브러리를 통해 v3에서도 충분히 사용할 수 있었다.

## 📚 라이브러리 설치하기

이번에 사용한 라이브러리는 `@tailwindcss/container-queries` 다.

먼저 설치부터 하자.

```bash
npm install @tailwindcss/container-queries
```

`tailwind.config.ts` 파일에 해당 플러그인을 사용하겠다고 작성한다.

```typescript
// tailwind.config.js
module.exports = {
  theme: {
    // ...
  },
  plugins: [
    require("@tailwindcss/container-queries"),
    // ...
  ],
}
```

나는 딱 두가지의 container `breakpoint` 가 필요했기에 아래와 같이 작성해주었다.

```typescript
theme: {
    containers: {sm: '640px', md: '768px'},
    // ...
}
```

## 💡 라이브러리 사용하기

사용 방식도 굉장히 간단하다!

### ✅ 기본 사용법

먼저 container (기준)이 되어야 하는 elements에 `container`를 명시해주고, 해당 `container`크기에 따라 달라져야 하는 요소들에 `@사이즈:클래스` 를 작성해주면 된다!!

```typescript
<div class="@container">
  <div class="@lg:underline">lg 사이즈 이상일 때 underline이 생깁니다!</div>
</div>
```

### ✅ 이름 지정하기

`@container/container이름` 을 통해 기준이 될 특정 `container` 를 만들어 주고 실제 사용할 때는 `@사이즈:클래스/container이름` 과 같이 작성해서 사용한다.

```typescript
<div class="@container/main">
  <div class="@lg/main:underline">
    main container가 lg 사이즈 이상일 때 underline이 생깁니다!
  </div>
</div>
```

### ✅ 임의의 크기 지정하기

기본적으로 제공되는 컨테이너 크기 뿐만 아니라 임의의 값을 사용해 일회성 사이즈를 지정해줄 수도 있다.

```typescript
<div class="@container">
  <div class="@[17.5rem]:underline"></div>
	  container가 17.5rem 사이즈 이상일 때 underline이 생깁니다!
  </div>
</div>
```

### ✅ container 삭제하기

container가 더이상 container의 역할을 하지 않도록 하고 싶다면 `@container-normal`을 사용하면 된다.

# 👩‍💻 custom Hook 변경

현재 작업한 내용 중에는 custom Hook을 사용해, `resize` 이벤트가 발생했을 때, 적절한 사이즈가 되면 필요한 컴포넌트를 렌더링 하도록 했다.

container Query를 사용하기 위해선 해당 customHook을 `container` 로 삼을 `elements` 의 `offsetWidth`를 추적할 필요가 있었다.

그런데.. 창 사이즈를 직접 줄이는 것이 아니라 특정 element의 크기를 줄이는 것인데 이를 어떻게 감지하지? 🤔 고민하던 와중 `ResizeObserver` 를 발견했다.

```typescript
import { useState, useEffect } from "react"

//자주 사용하는 container 의 breakPoint
//  768px, 640px
const useIsSmallContainer = (breakpoint: number) => {
  const [isSmallContainer, setIsSmallContainer] = useState(false)

  useEffect(() => {
    const container = document.querySelector("[data-content]")
    if (!container) return

    //특정 요소의 크기 변화를 감지한다.
    const observer = new ResizeObserver(([entry]) => {
      if (!entry) return
      const width = entry.contentRect.width
      setIsSmallContainer(width < breakpoint)
    })

    observer.observe(container)

    return () => observer.disconnect()
  }, [breakpoint])

  return isSmallContainer
}

export default useIsSmallContainer
```

# 😢 해결하기 어려웠던 상황

그런데, 이미 tailwind의 기본 `md`사이즈를 작성해 둔걸 `@md` 로 교체하는 것 만으로는 문제가 발생했다. 왜냐하면 뷰포인트 기준으로 4가지의 breakpoint가 있는데, B컴포넌트 안에 있는 `B-1` 컴포넌트는 특정 사이즈를 유지하다가 줄어들어야 하기 때문이었다.

그래서 이 부분은 그냥 CSS로 처리하는 것이 더 편리할 것 같다는 판단하에, 아래와 같이 작성했다.

주의할 점은 `@container`안에 `@media`를 중첩시키는 것은 가능하지만 반대는 불가능 하다는 것이다.

```typescript
/* container query와 media query 두 조건을 계산해야 되는 경우 */
.container {
	container-type: inline-size;
}

@container (max-width: 639px) {
	@media (min-width: 1280px) {
		.this-content span {
			@apply text-basic;
		}
	}
}
@container (max-width: 749px) {
	@media (min-width: 1280px) {
		.box-container {
			@apply px-4;
		}
	}
}
```

# 🌟 결과

결과적으로 라이브러리로 해결할 수 있는 부분은 최대한 CSS 작성 없이 라이브러리로 처리하여 최소한의 CSS만 작성하고, 나머지는 Tailwind를 사용하여, A와 C컴포넌트 크기가 변경했을 때 B컴포넌트 사이즈가 적절히 대응하여 더 나은 사용성을 제공할 수 있었다.

## ✅ 체크 포인트

1. 지금은 라이브러리를 사용했지만, Tailwind v4를 사용할 수 환경이 구축되면 더 쉽고 빠르게 Container Query를 사용할 수 있을 것이라 기대된다.
2. Container Query를 실제 사용해보는건 처음이었는데, 이전에 유튜브를 통해 접한 내용이라 필요한 상황에 적절히 떠올릴 수 있었다. 역시 아는게 힘!

## 🗂️참고 사이트

- <https://www.npmjs.com/package/@tailwindcss/container-queries>
