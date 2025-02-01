---

title: "TailwindCSS (7) - Tailwind 4.0 업데이트 정리"
author: saemii
categories: - TailwindCSS
tags: - [Next.js, TailwindCSS]
date: 2024-01-24
last_modified_at: 2024-01-24
pin: true

---

## 📌시작하며

tailwind CSS가 4.0으로 업데이트 되었다! tailwind CSS가 3.0으로 업데이트 된게 2021년 12월이니, 3년 조금 넘어 1버전이 업데이트 된 것!😎
나는 tailwind를 기본으로 작업을 하기 때문에 이번에 어떤 내용들이 업데이트 되었는지 무척 기대가 된다.
그럼 [블로그 글](https://tailwindcss.com/blog/tailwindcss-v4)을 바탕으로 업데이트 내역을 살펴보고,
실제 학습용 repo에 사용해보면서 공부한 내용을 함께 정리하고자 한다.

## ✅tailwind CSS 4.0

테일윈드 CSS v4.0은 성능과 유연성에 최적화된 **완전히 새로운 버전**의 프레임워크로, 새로운 구성과 사용자 정의 환경을 제공하며, 웹 플랫폼이 제공하는 최신 기능을 최대한 활용한다고 설명한다.

### 🌷설치 / 업데이트

현재 내 repo는 next.js latest버전을 기반으로 tailwind 3버전이 설치되어있었는데, 공식문서의 upgrade guide를 따라 업그레이드 해주었다.

```bash
npx @tailwindcss/upgrade@next
```

설치후 확인하니 아래와 같이 4버전으로 업그레이드 되었다.

```bash
"@tailwindcss/postcss": "^4.0.0",
```

`postcss.config.js` 파일에 아래와 같이 적어주자.

```javascript
//postcss.config.js
module.exports = {
  plugins: {
    "postcss-import": {},
    tailwindcss: {},
    autoprefixer: {},
    "@tailwindcss/postcss": {},
  },
}
```

그리고 global.css에 tailwind css를 import 해준다.
기존과 다르게 `@import "tailwindcss"` 한문장만 작성하는 것으로 변경되었다.

```css
/* globals.css */
/* 
@tailwind base;
@tailwind components;
@tailwind utilities; 
*/

@import "tailwindcss";
```

마지막으로 `dev`모드에서 tailwind가 올바르게 적용되는지 확인해준다.

## ✅이름이 변경된 유틸리티

v4에서 몇가지 유틸리티의 이름이 변경되었다.

| v3               | v4               |
| ---------------- | ---------------- |
| shadow-sm        | shadow-xs        |
| shadow           | shadow-sm        |
| drop-shadow-sm   | drop-shadow-xs   |
| drop-shadow      | drop-shadow-sm   |
| blur-sm          | blur-xs          |
| blur             | blur-sm          |
| backdrop-blur-sm | backdrop-blur-xs |
| backdrop-blur    | backdrop-blur-sm |
| rounded-sm       | rounded-xs       |
| rounded          | rounded-sm       |
| outline-none     | outline-hidden   |
| ring             | ring-3           |

특징을 보자면, `shadow`, `blur` 처럼 단위가 없는 것들에 **단위가 생기면서** 기존의 sm이 xs로 변경되었음을 알 수 있다. 개인적으로는 관련된 모든 유틸리티의 이름이 통일성을 가지게 되었다고 생각해 만족하는 변경점이다. 😊

### 🌷 ring

- v3: `ring`을 쓰면 3px 효과가 나타났다.
- v4: `ring-3` 이란 클래스가 생겨났고, `ring`은 **1px**이다.

참고로, `ring`과 `ring-1` 모두 동일하게 1px의 효과가 나타나는 것을 확인했다. 개발도구로 확인했을 때 동일한 class가 사용된다.

```javascript
export default function TailwindFour() {
  return (
    <div className="h-dvh w-full p-6">
      <div className="flex flex-col gap-10">
        <button className="px-4 py-2 bg-red-400 ring">버튼입니다</button>
        <button className="px-4 py-2 bg-blue-400 ring-1">버튼입니다</button>
        <button className="px-4 py-2 bg-green-400 ring-3">버튼입니다</button>
      </div>
    </div>
  )
}
```

### 🌷기본 border 컬러 변경

- v3: 기본적으로 `border` 의 색상이 `gray-200` 이었다.
- v4: `currentColor`와 동일한 색상으로 설정된다. (브라우저의 기본 동작과 일치함)

### 🌷기본 placeholder 컬러 변경

- v3: 기본적으로 `placeholder` 의 색상이 `gray-400` 이었다.
- v4: `currentColor`의 50% opacity로 설정된다.

## ✅새로운 고성능 엔진

전체 빌드는 최대 5배, **증분 빌드**는 100배이상 빨라졌다.

> **증분 빌드(incremental builds)**
> 소프트웨어 빌시 변경된 부분만 빌드하는 방법

## :star:CSS 중심의 통합적인 커스텀 방식

큰 변화 중 하나인데, 프로젝트를 구성할 때 `tailwind.config.js` 의 JavaScript를 사용하는 대신, CSS로 구성하도록 변경되었다.

### 🌷3.0 버전의 tailwind.config.js

3.0버전의 경우 아래와 같이 파일을 작성해서 tailwind를 설정해주었다.
아래는 `lime`이란 color를 정의해준 예시이다.

```javascript
import type { Config } from "tailwindcss";

export default {
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    extend: {
      colors: {
        'line': "#B2F300",
      },
    },
  },
  plugins: [],
} satisfies Config;

```

### 🌷4.0 버전의 CSS

`globals.css`에 다음과 같이 작성하자.

```css
@import "tailwindcss";

@theme {
  --color-lime: #b2f300;
}
```

해당 구문을 해석하면 <u>color에 lime이란 이름을 가진 색을 추가하겠다</u> 가 되고,
실제 사용방식은 동일하다.

```html
<button className="px-4 py-2 bg-lime ring-3">버튼입니다</button>
<button className="px-4 py-2 text-lime ring-3">버튼입니다</button>
```

그러면 위에서 정의한 lime 색이 정확히 적용된 모습을 보인다!
추가로, 4.0에서의 ring 컬러는 `currentColor`가 적용되어, text 색상인 lime 색과 동일한 색을 띄게 된다.

## :star:keyframe animation 정의하기

이번엔 animation을 정의해보자. 위와 동일하게 CSS에서 작성한다.

keyframe을 정의하는 방식은 기존 CSS 작성방식과 동일하며,
`@theme`에 사용 등록만 해주면 된다.

```css
@import "tailwindcss";

@theme {
  --color-lime: #b2f300;

  --animate-scale-up: scale-up 2s ease-out;
}

@keyframes scale-up {
  0% {
    transform: scale(0);
  }
  100% {
    transform: scale(1);
  }
}
```

실제 컴포넌트에 적용은 다음과 같다.

```html
<div className="size-20 bg-lime animate-scale-up "></div>
```

## ✅@container queries

CSS의 최신 기능인 container queries의 기능이 추가 되었다.

### 🌷container queries

컨테이너 쿼리는 뷰포트를 기준으로 하는 `@media` 쿼리와 다르게, 요소의 컨테이너 크기에 따라 스타일을 지정할 수 있다.
아래 예제를 살펴보자.

먼저 `globals.css`에 다음과 같이 컨테이너 크기를 지정해주었다.

```css
@import "tailwindcss";

@theme {
  --container-sm: 100px;
  --container-md: 200px;
}
```

컴포넌트를 이어서 살펴보자. 해당 예제는 부모 `div`를 클릭하면 **컨테이너** 크기가 변경되며,
`@sm`과 `@md`로 컨테이너 사이즈에 따라 조건부 css를 적용했다.

즉, container 사이즈가 `@sm`일때 `text-red-500`과 `text-xs`를 적용하며
container가 `@md` 인 경우엔 `text-blue-500`과 `text-2xl`이 적용된다.

```javascript
const ContainerQueries = () => {
  const [isSmall, setIsSmall] = useState<boolean>(false);

  return (
    {/* 클릭 시 컨테이너 크기 변경*/}
				<div
					className={cn('@container bg-lime cursor-pointer ', {
						'w-[100px]': isSmall,
						'w-[200px]': !isSmall,
					})}
					onClick={() => {
						setIsSmall(!isSmall);
					}}>
					{/* 컨테이너 크기에 따라 글씨 색과 크기가 변경됨 */}
					<div
						className={`text-center @sm:text-xs @sm:text-red-500 @md:text-blue-500 @md:text-2xl text-black`}>
						{isSmall
							? '컨테이너가 작아져, 글씨가 작아졌습니다.'
							: '컨테이너가 커져 글씨가 커졌습니다.'}
					</div>
				</div>
  )
}
```

`min` `max`값을 지정해줄 수 있다.

```javascript
<div
  className={cn("@container bg-gray-200 ", {
    "w-[50px]": size === "xs",
    "w-[100px]": size === "sm",
    "w-[200px]": size === "md",
    "w-[300px]": size === "lg",
  })}
>
  <div className="@sm:text-red-500 @md:text-green-500 @lg:text-blue-500 @xs:@max-md:text-2xl">
    현재 {size} 사이즈에 맞는 CSS가 적용되었습니다.
  </div>
</div>
```

예제와 같이 `@xs:@max-md:text-2xl`을 이용해 `xs~md`사이즈까지만 특정 css를 적용해 줄 수 있다.

## ✅@starting-style

CSS의 `@starting-style` 의 기능이 들어왔다. 처음보는 기능이라 [MDN 사이트](https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style)에 들어가봤는데, 아직 Firefox에서는 사용이 불가능했지만, 대부분의 브라우저에서 사용 가능했다.

`@starting-style`은 요소가 처음 DOM에 렌더링되거나, `display: none`에서 `visible`로 전환될 때 초기 상태를 설정하는 데 사용된다. 즉 요소가 처음 표시될 때 스타일을 지정하거나 부드러운 애니메이션을 넣어줄 수 있다.

이 설명에 바로 떠오르는 것은 바로 popup이나 modal창이다.

자세한 사항은 다음 포스팅을 참고한다.

## :star:gradient 확장

그라디언트를 더욱 다양하게 활용할 수 있는 유틸리티가 추가되었다.

gradient에 linear만 지원하는 것이 아닌 다른 형태도 함께 지원하면서 기존의 linear형태의 gradient의 경우 `bg-gradient-*`에서 `bg-linear-*`로 이름이 변경되었다!

### 🌷선형 그라디언트와 각도

선형 그라디언트에 각도를 쉽게 넣을 수 있다.
사용 방식은 `linear-각도`와 같다.

사용자가 원하는 각도의 경우 `bg-linear-[190deg]`와 같은 방식으로 작성한다.

```javascript
import React from "react"

const Gradient = () => {
  return (
    <div className=" w-full p-6">
      <h1 className="text-2xl font-semibold">그라디언트</h1>
      <h2 className="text-xl font-semibold mt-5">각도</h2>
      <div className="**:size-20 **:rounded-sm **:text-white **:via-purple-500 **:to-pink-500 **:from-indigo-500 space-y-2">
        <div className="bg-linear-45">45</div>
        <div className="bg-linear-90">90</div>
        <div className="bg-linear-180">180</div>
        <div className="bg-linear-[190deg]">190</div>
        <div className="bg-linear-270">270</div>
      </div>
    </div>
  )
}

export default Gradient
```

### 🌷색상 제어

[색상 보간(interpolation)](https://developer.mozilla.org/ko/docs/Glossary/Interpolation) 방식을 제어할 수 있는 모디파이어(modifier)가 추가되었다.

> **색상 보간**
> 보간이란, 알려진 값을 기반으로 값을 계산하는 프로세스를 의미한다. 그라디언트에서는 제공된 색상 목록을 기반으로 색상의 중간 값을 정의하는 데 사용된다.

작성할 때는 다음과 같이
`bg-linear-to-r/srgb`, `bg-linear-to-r/oklch`와 같이 작성한다.
tailwind 4.0에서는 oklch 방식이 기본 적용되기 떄문에, oklch 방식을 사용할 땐 굳이 작성하지 않아도 된다.

```javascript
import React from "react"

const Gradient = () => {
  return (
    <div className=" w-full p-6">
      <h2 className="text-xl font-semibold mt-5">색상</h2>
      <div className="**:size-20 **:rounded-sm **via-purple-500 **:to-pink-500 **:from-indigo-500 space-y-2  **:text-white">
        <div className="bg-linear-to-r/srgb">srgb</div>
        <div className="bg-linear-to-r/oklch">oklch</div>
      </div>
    </div>
  )
}

export default Gradient
```

### 🌷원뿔형과 방사형

원뿔형, 방사형 그래디언트를 생성하기 위한 유틸리티가 추가되었다.
`bg-conic-_`, `bg-radial-_` 로 작성하면 된다.

```javascript
import React from "react"

const Gradient = () => {
  return (
    <div className=" w-full p-6">
      <h2 className="text-xl font-semibold mt-5">원뿔형</h2>
      <div className="size-20 rounded-sm bg-conic/[in_hsl_longer_hue] from-red-600 to-red-600">
        원뿔
      </div>
      <h2 className="text-xl font-semibold mt-5">방사형</h2>
      <div className="size-20 rounded-full bg-radial-[at_25%_25%] from-white to-zinc-900 to-75%">
        방사
      </div>
    </div>
  )
}

export default Gradient
```

## :star:자식요소 CSS

자식요소에 한 번에 CSS를 줄 수 있는 방법이 생겨났다!

### 🌷직계 요소

직계 요소는 `*:~`를 사용해 작성한다.

아래 예제에서는 이 규칙을 이용해 `<li>` 태그에 스타일을 주었다.

```javascript
export default function Children() {
  return (
    <Container>
      <Container.Title>자식요소</Container.Title>
      <Container.SubTitle>직계</Container.SubTitle>
      <div className="mt-3">
        <ul className="flex gap-2 *:rounded-full *:border *:border-sky-100 *:bg-sky-50 *:px-2 *:py-0.5 *:text-sky-500">
          {hutaba.map((item, index) => {
            return <li key={item.name}>{item.name}</li>
          })}
        </ul>
      </div>
    </Container>
  )
}
```

### 🌷자손 요소

아래 자손 요소 모두에 스타일을 주고 싶다면 `**:~` 규칙을 사용한다.

이때, `data-*` 규칙을 이용해 특정 tag들만 CSS를 줄 수 있다!
아래는 `<p>`태그에 `data-birth` 라고 이름을 지어 주고, `ul` 태그에 `**:data-birth:text-gray-500` 와 같은 방식으로 한 번에 스타일을 주었다.

```javascript
export default function Children() {
  return (
    <Container>
      <Container.SubTitle>모든 자손</Container.SubTitle>
      <ul className="**:data-avatar:size-12 **:data-avatar:rounded-full **:data-birth:text-gray-500 **:data-birth:text-xs **:text-sky-500 space-y-2 mt-3">
        {hutaba.map((item, index) => {
          return (
            <li key={item.name} className="flex items-center gap-4">
              <div data-avatar className="border overflow-hidden">
                <Image
                  src={item.src}
                  alt={item.name}
                  width={100}
                  height={100}
                />
              </div>
              <div>
                <p>{item.name}</p>
                <p data-birth className="">
                  {item.birth}
                </p>
              </div>
            </li>
          )
        })}
      </ul>
    </Container>
  )
}
```

## ✅not

`not`을 이용해 특정 상황이 아닐 때만 CSS를 줄 수 있다!
특히 **checked가 되지 않았을 때(not)** 와 같은 상황에 활용도가 좋다.

```javascript
export default function Not() {
  return (
    <Container>
      <Container.Title>not</Container.Title>
      <div className="space-y-5">
        <div className="not-hover:opacity-75 bg-blue-500 p-4 text-white cursor-pointer">
          마우스를 올리지 않으면 투명도 75%
        </div>
        <label className="flex items-center space-x-2">
          <input type="checkbox" id="apple" className="hidden peer" />
          <div className="peer-not-checked:bg-red-500 peer-checked:bg-green-500 text-white px-4 py-2 cursor-pointer">
            체크하면 초록색, 해제하면 빨간색
          </div>
        </label>
      </div>
    </Container>
  )
}
```

## ✅Dynamic Utility

4.0버전에서는 더 다양한 **임의의 값**을 쓸 수 있게 변경되었다.

예를 들어, grid 컬럼을 15개를 쓰고 싶을 때, 기존 3버전에서는 해당 값이 tailwind에 미리 등록되지 않았으므로,
`grid-cols-[15]`와 같이 작성해야 했으나, 이제는 그냥 `grid-cols-15`로 써도 알아서 처리된다.

동일하게, `mt-11`이나 `mt-13`과 같이 기존에는 사용할 수 없던 class도 알아서 처리된다!

```javascript
import Container from "@/components/Container"

export default function Dynamic() {
  return (
    <Container>
      <Container.Title>Dynamic Utility</Container.Title>
      <div className="grid grid-cols-15 gap-2 **:size-4 **:text-xs **:text-white **:bg-blue-500">
        {Array.from({ length: 30 }).map((_, index) => (
          <div key={index}>{index + 1}</div>
        ))}
      </div>
      <div className="mt-11 grid grid-cols-17 gap-2 **:size-4 **:text-xs **:text-white **:bg-red-500">
        {Array.from({ length: 30 }).map((_, index) => (
          <div key={index}>{index + 1}</div>
        ))}
      </div>
      <div className="mt-13 grid grid-cols-11 gap-2 **:size-4 **:text-xs **:text-white **:bg-green-500">
        {Array.from({ length: 30 }).map((_, index) => (
          <div key={index}>{index + 1}</div>
        ))}
      </div>
    </Container>
  )
}
```

이게 가능한 이유는, Tailwind의 **기본 간격 단위인 --spacing**을 기반으로 자동 계산되기 때문이다.

```css
@layer theme {
  :root {
    --spacing: 0.25rem;
  }
}
@layer utilities {
  .mt-8 {
    margin-top: calc(var(--spacing) * 8);
  }
  .w-17 {
    width: calc(var(--spacing) * 17);
  }
  .pr-29 {
    padding-right: calc(var(--spacing) * 29);
  }
}
```

## 👏마무리 하며

결론부터 말하자면, 정말 마음에 드는 업데이트다! 자손 요소에 한 번에 CSS를 지정해주는 게 너무 편리해서 얼른 도입하고 싶을정도....

그런데, 이 포스팅 연습용으로 사용한 repo는 vercel로 github push때마다 배포를 시도하게 해두었는데, tailwind 4.0 업데이트 하자 배포가 안되었다(!)
기존에 설치해둔 tailwind 관련 eslint 라이브러리와 tailwind 4.0이 호환되지 않았기 때문이다...🤔

언제쯤 업데이트가 될지 궁금해서 해당 라이브러리의 깃허브를 찾아가 보니 이미 많은 사람들이 업데이트에 대해 문의해두었으나,
시간이 다소 소요될 것이라는 답이 달려있었다.😅
나의 여가 생활에 하는 프로젝트이기 때문이다 라는 말에 (그건 그렇네요...)하게 되는 것은 덤 (ㅎㅎ)

코드 품질을 위한 tailwind의 eslint를 적용하는 것에 관련된 라이브러리기 때문에 아예 없는 상태에서 대형 프로젝트를 시작하는 것은
피하고 싶어서 아무래도, 실제 프로젝트 도입은 조금 더 미뤄지게 되겠지만,
개인적으로 진행할 때는 적극적으로 사용할 계획이다!

이번 업데이트는 공식 릴리즈 이틀 후에 공식 docs를 보면서 예제를 만들어 보고, 기록해보았는데
훨씬 이해하기도 쉽고, 몰랐던 기존 tailwind 기능을 더 많이 알 수 있어서 너무 만족스러웠다.😊

회사에서, 이 기능을 공유할 수 있길 바라며, 이번 포스팅은 여기서 끝!

## 🗂️참고 사이트

- <https://learn.microsoft.com/ko-kr/visualstudio/msbuild/incremental-builds?view=vs-2022>
- <https://tailwindcss.com/blog/tailwindcss-v4>
- <https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries>
