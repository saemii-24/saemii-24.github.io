---
title: "Nextjs-Optimizing(1) - Font"
author: saemii
categories:
  - Nextjs
tags:
  - [Nextjs]
date: 2025-02-26
last_modified_at: 2025-02-26
pin: true
---

## 📌시작하며

퇴근 전, 다른 프론트엔드 개발자 분과, 라이브러리 번들 사이즈에 대해 이야기를 나누었다. 최적화의 중요성은 너무 당연한 것이기도 해서, 번들 사이즈를 줄이는 것이나, 최적화 진행에 대해 공감했고, 기본적으로 알고있는 내용에 더해 Next js가 제공하는 최적화 방식도 알아두면 좋을 것 같아서, 한동안 Next js의 공식문서의 Optimizing 부분을 파헤쳐 볼 생각이다.😎

먼저 오늘은 가볍게(?) 기존에 자주 사용하던 `Next/font`에 관한 이야기다.

아래 내용은 공식문서를 번역하거나, 직접 사용해보고 추가한 내용이 담겨있다.

### 🔹Google Fonts

`next/font/google`를 사용하면, 애플리케이션의 빌드 과정에서 구글 폰트를 다운로드하여 로컬로 포함시키기 때문에, 폰트 파일은 빌드 결과물에 포함된다. 따라서, 사용자가 웹사이트를 방문할 때마다 구글 서버에 요청을 보내지 않고, 로컬 서버에서 해당 폰트가 제공되므로 개인정보 보호와 성능에 이점이 있다.

사용방식은 다음과 같다!

```typescript
import "./globals.css"
import Provider from "./Provider"
import Header from "@/components/Header"
import { Noto_Sans_KR } from "next/font/google"

// 다양한 weight의 글꼴(Variable font)을 로드하는 경우 따로 weight를 지정할 필요 없음!
const noto = Noto_Sans_KR({
  subsets: ["latin"],
  display: "swap",
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="ko" className={noto.className}>
      <body>
        <Provider>
          <Header />
          {children}
        </Provider>
      </body>
    </html>
  )
}
```

만약 특정 weight의 글꼴만 필요하다면, 아래와 같이 작성해준다.
이 경우에, 직접 CSS로 font-weight를 조정하려고 하더라도, `bold`와 `border`옵션 외에 weight가 변하지 않는다.

```typescript
const noto = Noto_Sans_KR({
  weight: "400",
  subsets: ["latin"],
  display: "swap",
})
```

```typescript
const noto = Noto_Sans_KR({
  weight: ["400", "700"],
  subsets: ["latin"],
  display: "swap",
})
```

### 🔹Tailwind와 함께 사용하기

사용할 폰트가 많으면, tailwind에 폰트를 정의해두고 사용하는 것이 편리하니 함께 사용해보자
먼저 폰트 내용을 정의해주었다. 여기서 중요한건 **variable** 로 각 폰트별로 이름을 지어준다.

```typescript
// styles/font.ts
import { Noto_Sans_KR, Roboto, Source_Sans_3 } from "next/font/google"
import localFont from "next/font/local"

// 변수 폰트 정의
const noto = Noto_Sans_KR({
  variable: "--font-noto",
  subsets: ["latin"],
})
const roboto = Roboto({
  weight: "400",
  subsets: ["latin"],
  variable: "--font-roboto",
})
// 특정 weight을 가진 폰트 정의
const sourceCodePro400 = Source_Sans_3({
  weight: "400",
  variable: "--font-source-400",
  subsets: ["latin"],
})
const sourceCodePro700 = Source_Sans_3({
  weight: "700",
  variable: "--font-source-700",
  subsets: ["latin"],
})
// 로컬 폰트 정의
const pretendard = localFont({
  src: "../app/font/Pretendard-Regular.otf",
  variable: "--font-pretendard",
})

export { noto, roboto, sourceCodePro400, sourceCodePro700, pretendard }
```

그 다음 최상위 layout.tsx의 className에 위에서 return 해준 값들을 넣어주었다.

```typescript
// app/layout.tsx
import "./globals.css"
import Provider from "./Provider"
import Header from "@/components/Header"
import { Noto_Sans_KR } from "next/font/google"
import {
  noto,
  roboto,
  sourceCodePro400,
  sourceCodePro700,
  pretendard,
} from "@/styles/font"

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html
      lang="ko"
      className={`${noto.variable} ${roboto.variable} ${sourceCodePro400.variable} ${sourceCodePro700.variable} ${pretendard.variable}`}
    >
      <body>
        <Provider>
          <Header />
          {children}
        </Provider>
      </body>
    </html>
  )
}
```

마지막으로 `tailwind.config.ts`에 작성해주자. 참고로 해당 내용은 tailwind 3버전 기준으로 작성되었는데, 한동은 3버전을 사용할 예정이긴 하지만, 4버전 도입시 어떻게 작성하면 좋을지는 4버전을 사용하는 레포지토리에서 확인해보는게 좋을 듯 하다.🤔

```typescript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./components/**/*.{js,ts,jsx,tsx,mdx}",
    "./app/**/*.{js,ts,jsx,tsx,mdx}",
    "./app/(root)/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    extend: {
      fontFamily: {
        noto: ["var(--font-noto)"],
        roboto: ["var(--font-roboto)"],
        sourceCodePro: ["var(--font-source-400)"],
        sourceCodeProBold: ["var(--font-source-700)"],
        pretendard: ["var(--font-pretendard)"],
      },
    },
  },
  plugins: [],
}
```

실제 사용은 다음과 같이 위에서 정의해준 이름을 바탕으로 `font-이름`으로 작성해주면 된다.

```typescript
export default function Page() {
  return (
    <>
      <p className="font-noto">
        폰트가 적용되었는지 살펴볼까요?
        <br />
        Let&apos;s check if the font has been applied properly!
      </p>
      <p className="font-roboto">
        폰트가 적용되었는지 살펴볼까요?
        <br />
        Let&apos;s check if the font has been applied properly!
      </p>
    </>
  )
}
```

### 🔹CSS와 함께 사용하기

만약 Tailiwnd가 아니라 CSS파일에서 사용해야 한다면, `styles/font.ts`와 `app/layout.tsx`까지는 위와 같은 방식으로 작성하고, global.css에 다음과 같이 작성한다.

```typescript
html {
  font-family: var(--font-noto);
}

h1 {
  font-family: var(--font-roboto);
}
```

## 💌Font Module

Next js의 API Reference를 통해 어떻게 `next/font/google` 과 `next/font/local`을 사용하는지 자세히 알아보자.

| Key                  | font/google | font/local | Type                | Required  |
| -------------------- | ----------- | ---------- | ------------------- | --------- |
| `src`                | ❌          | ✅         | 문자열 or 객체 배열 | 필수      |
| `weight`             | ✅          | ✅         | 문자열 or 배열      | 필수/선택 |
| `style`              | ✅          | ✅         | 문자열 or 배열      | -         |
| `subsets`            | ✅          | ❌         | 문자열 배열         | -         |
| `axes`               | ✅          | ❌         | 문자열 배열         | -         |
| `display`            | ✅          | ✅         | 문자열              | -         |
| `preload`            | ✅          | ✅         | 불리언              | -         |
| `fallback`           | ✅          | ✅         | 문자열 배열         | -         |
| `adjustFontFallback` | ✅          | ✅         | 불리언 or 문자열    | -         |
| `variable`           | ✅          | ✅         | 문자열              | -         |
| `declarations`       | ✅          | ✅         | 객체 배열           | -         |

### 💟src

폰트 파일의 경로를 문자열 또는 객체 배열로 지정한다.

### 💟weight

폰트의 두께를 지정한다.

### 💟style

### 💟subsets

> subsets: 전체 글꼴 파일에서 특정 문자나 기호만을 포함하는 경량화된 폰트 파일

`next/font/google`를 사용할 때, `subsets` 옵션을 통해 미리 로드하고 싶은 글꼴 서브셋을 정의할 수 있다. preload 옵션이 true로 설정되어 있다면(default값은 true), 이 서브셋의 `<link rel="preload"`> 태그가 `<head>`에 자동으로 삽입된다!

폰트에 따라 지원되는 subset이 달라지기 때문에, Google Fonts의 사용 중인 글꼴 페이지에서 확인할 수 있으며, 모든 글꼴이 포함된 파일이 아니라, 필요한 서브셋만 로드해 폰트 최적화가 가능하다.

아쉬운 점은 한글 서브셋은 없는 것으로 보인다는 것.. 😅

Noto_Sans_KR이 지원하는 서브셋 목록은 다음과 같다.

> Available subsets: `cyrillic`, `latin`, `latin-ext`, `vietnamese`

| 서브셋 종류 | 설명                                                                |
| ----------- | ------------------------------------------------------------------- |
| cyrillic    | 러시아어, 우크라이나어, 불가리어 등--                               |
| latin       | 영어, 프랑스어, 독일어, 스페인어 등                                 |
| latin-ext   | latin의 확장 버전, 주로 동유럽 언어로 폴란드어, 체크어, 헝가리어 등 |
| vietnamese  | 베트남어                                                            |

### 💟axes

### 💟display

글꼴을 로드할 때 브라우저가 어떻게 처리할지를 정해줄 수 있다.

| 전략         | 차단 기간 (Blocking Period) | 교체 기간 (Swap Period)   | 설명                                                               |
| ------------ | --------------------------- | ------------------------- | ------------------------------------------------------------------ |
| **auto**     | 브라우저 기본 설정에 따름   | 브라우저 기본 설정에 따름 | 브라우저의 기본 동작에 따름                                        |
| **block**    | 긴 차단 기간                | 무제한 교체               | 글꼴이 로드될 때까지 텍스트를 표시하지 않음. 로드된 후 글꼴 교체됨 |
| **swap**     | 매우 짧은 차단 기간         | 무제한 교체               | 텍스트는 기본 글꼴로 표시되며, 글꼴이 로드되면 빠르게 교체         |
| **fallback** | 매우 짧은 차단 기간         | 짧은 교체 기간            | 텍스트는 기본 글꼴로 표시되며, 글꼴이 로드되면 빠르게 교체         |
| **optional** | 매우 짧은 차단 기간         | 짧은 교체 기간            | 글꼴이 로드되지 않으면 기본 글꼴로 계속 표시됨 (성능 최적화)       |

### 💟preload

### 💟fallback

### 💟adjustFontFallback

### 💟variable

### 💟declarations

## 💌폰트 경로 파일 사용하기

localFont나 googleFont를 사용할 때 그 폰트는 하나의 인스턴스로 호출된다! 따라서, 매번 폰트를 요청하는 것이 아니라, 한 파일에 다양한 폰트를 요청하고, 그 모아놓은 파일을 import해서 사용하는 것이 좋다.

- <https://nextjs.org/docs/app/building-your-application/optimizing/fonts>
- <https://nextjs.org/docs/app/api-reference/components/font>
- <https://developer.mozilla.org/ko/docs/Web/CSS/@font-face/font-display#values>
- <https://www.youtube.com/watch?v=L8_98i_bMMA>
