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

## ✅기본 border 컬러 변경

- v3: 기본적으로 `border` 의 색상이 `gray-200` 이었다.
- v4: `currentColor`와 동일한 색상으로 설정된다. (브라우저의 기본 동작과 일치함)

## ✅기본 placeholder 컬러 변경

- v3: 기본적으로 `placeholder` 의 색상이 `gray-400` 이었다.
- v4: `currentColor`의 50% opacity로 설정된다.

## ✅새로운 고성능 엔진

전체 빌드는 최대 5배, **증분 빌드**는 100배이상 빨라졌다.

> **증분 빌드(incremental builds)**
> 소프트웨어 빌시 변경된 부분만 빌드하는 방법

## ✅최신 웹과 어울림

Tailwind CSS v3.0 출시 이후 플랫폼이 많이 발전하였고, v4.0은 이러한 개선 사항을 최대한 활용한다.

### 🌷Native cascade layers

다양한 스타일 규칙이 서로 상호 작용하는 방식을 잘 제어할 수 있게 해준다.

## 🗂️참고 사이트

- <https://learn.microsoft.com/ko-kr/visualstudio/msbuild/incremental-builds?view=vs-2022>
- <https://tailwindcss.com/blog/tailwindcss-v4>
