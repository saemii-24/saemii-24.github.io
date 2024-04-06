---
title: "Hydration failed because the initial UI does not match what was rendered on the server."
author: saemii
categories:
  - Error
tags:
  - [Error, Next.js, React]
date: 2024-04-06
last_modified_at: 2024-04-06
pin: true
---

## ⛔문제상황

localStorage와 tailwind를 이용해 darkmode를 구현하는 도 중 오류가 발생했다.

> Error: Hydration failed because the initial UI does not match what was rendered on the server.
> Warning: Expected server HTML to contain a matching <path> in <svg>.
> See more info here: <https://nextjs.org/docs/messages/react-hydration-error>

## ✅작성했던 로직

최초 기획은 사용자의 버튼 토글에 따라 모드를 선택하면, recoil을 이용한 전역상태관리를 통해 라이트/다크 모드를 구현하는 것이었다.

따라서 toggleTheme 함수를 통해 theme를 변경할 수 있는 버튼을 구현했다.

```typescript
<li className="cursor-pointer" onClick={handleToggleMode}👈>
  {theme === "dark" ? (
    <IoMoon
      onClick={() => {
        setTheme("light")
      }}
      className="text-lg text-music-bluegray"
    />
  ) : (
    <FiSun
      onClick={() => {
        setTheme("dark")
      }}
      className="text-lg text-music-bluegray"
    />
  )}
</li>
```

tailwind에서 dark 모드를 구현하기 위해서는 최상위인 html태그에 'dark' 클래스를 제어해야 하므로 아래와 같이 작성했다.

```typescript
const handleToggleMode = (): void => {
  if (typeof window !== "undefined") {
    localStorage.setItem("theme", theme)
    if (theme === "dark") {
      const htmlElement = document.querySelector("html") as HTMLElement
      htmlElement.classList.add("dark")
      setTheme("light")
    } else {
      const htmlElement = document.querySelector("html") as HTMLElement
      htmlElement.classList.remove("dark")
      setTheme("dark")
    }
  }
}
```

## ✅공식문서의 제안

에러를 해겨하기 위한 방법을 [공식문서](https://nextjs.org/docs/messages/react-hydration-error)에서 몇가지 제안했는데, 아래의 두 가지 모두 적용한 상태였고, 그 외에도 문제 될 상황이 없어보였다.🤔

```
Using checks like typeof window !== 'undefined' in your rendering logic
Using browser-only APIs like window or localStorage in your rendering logic
```

해결이 될 힌트는 '렌더링 시점'과 연관이 있다는 것이었고...

## ⭕해결

해결은 의외로 간단했다! 일단 관련 로직을 가다듬었다.
어차피 localStorage로 값을 받아오는 만큼 기존에 recoil로 전역상태관리를 할 필요가 없어졌기 때문에 이 부분을 삭제했다.

기존에 작성한 handleToggleMode는 삭제하고, useEffect를 이용하는 것으로 교체했다.

```typescript
// theme 값 가져오기
const initialTheme: ThemeType =
  typeof window !== "undefined"
    ? (localStorage.getItem("theme") as ThemeType) || "light"
    : "light"
const [theme, setTheme] = useState<ThemeType>(initialTheme)

useEffect(() => {
  if (typeof window !== "undefined") {
    localStorage.setItem("theme", theme)
    if (theme === "dark") {
      const htmlElement = document.querySelector("html") as HTMLElement
      htmlElement.classList.add("dark")
    } else {
      const htmlElement = document.querySelector("html") as HTMLElement
      htmlElement.classList.remove("dark")
    }
  }
}, [theme])
```

## 🗂️참고 사이트

-<https://nextjs.org/docs/messages/react-hydration-error>
