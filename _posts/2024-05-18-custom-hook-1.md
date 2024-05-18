---
title: "커스텀 훅으로 가독성 높은 코드로 리팩토링하기"
author: saemii
categories:
  - Optimize
tags:
  - [Optimize, Next.js, Project]
date: 2024-05-18
last_modified_at: 2024-05-18
pin: true
---

## 📌시작하며

최근에는 계속해서 프론트엔드 기술 중 "최적화"에 초점을 맞춰 공부하고 있다. 이 최적화 단계에는 사용자 경험(UX)를 위한 일을 포함하는 건 물론, 코드 최적화 단계를 포함한다. 이번 포스팅에서는 커스텀 훅으로 가독성과 유지보수성을 높일 수 있는 방법을 정리하고자 한다.

## ✅Hook이란?

리액트를 처음 접했을 당시 읽었던 책들은 앞에서는 class형을 뒤에서는 함수형 컴포넌트를 소개하고 있었다. 예제를 따라 쳐보면서도 class형보다 함수형이 훨씬 이해하기 쉽고 코드도 간결했는데, 함수형 컴포넌트가 가진 특징 중 하나는 바로 Hook을 사용한다는 것이었다.

Hook은 리액트 16.8버전에 도입된 기능으로 함수형 컴포넌트에서도 상태와 생명주기 기능을 사용하게 해준다. Hook을 사용하면 컴포넌트로부터 **상태 관련 로직을 추상화**할 수 있으며, 덕분에 독립적인 테스트와 재사용이 가능해진다.

## ✅추상화란?

위에서 등장한 '추상화'란 단어에 집중해보자. 커스텀 훅도 'Hook'의 일종인 만큼 추상화를 가능하게 하는 역할을 한다. 그럼 여기서 말하는 **추상화**란 무엇인가?

> 컴퓨터 과학에서 추상화(abstraction)는 복잡한 자료, 모듈, 시스템 등으로부터 핵심적인 개념 또는 기능을 간추려 내는 것을 말한다.
> 출처: [위키백과](<https://ko.wikipedia.org/wiki/%EC%B6%94%EC%83%81%ED%99%94_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99)>)

즉, 쉽게 이해해보자면 복잡한 코드를 간결하게 만들어 중요한 내용에 집중하게 하고, 세부적인 부분을 숨기는 것이다. `useState`를 생각해보자. 우리가 사용할 때는 `const [state, setState] = useState()` 이렇게 작성하고 바로 사용하면 되지만, useState가 **그렇게 작동하기 위해서** 작성된 코드는 훨씬 길고 복잡할 것이다!

### ➡️추상화의 장점

이렇게 코드를 추상화하면 어떤 장점이 있을까?

**1. 코드의 재사용성 증가**

- 추상화 된 코드는 독립적인 모듈로 분리되어 여러 곳에서 재사용 될 수 있다.

**2. 유지보수성 향상**

- 모듈화 된 코드의 경우 변경이 필요할 경우 특정 부분만 수정해서 전체에 적용할 수 있다.

**3. 복잡성 감소**

- 시스템의 복잡한 부분을 감추고 중요한 내용에 집중할 수 있어 코드의 복잡성이 감소하고 가독성이 좋아진다.

## ✅Custom hook 만들기

이런 장점이 있는 Hook은 자신의 코드에 적용해서 만들 수 있고, 이걸 Custom hook이라고 부른다. 그럼, 예제를 살펴보자!

### ➡️작명 규칙

커스텀 훅을 만들 때 주어진 작명 규칙을 준수해야 한다.

1. React 컴포넌트의 이름은 항상 대문자로 시작해야 한다.(예시 : StatusBar, SaveButton) 또한 React 컴포넌트는 JSX와 같이 React가 확인할 수 있는 무언가를 반환해야 한다.
2. Hook의 이름은 use 뒤에 대문자로 시작해야 한다. (예시 : useState, useOnlineStatus) Hook들은 어떤 값이든 반환할 수 있다.

### ➡️전체적인 틀

커스텀 훅을 작성하는 전체적인 틀은 다음과 같다. 데이터를 fetch 하는 useFetch 훅을 만들어 보자!

```javascript
// useFetch.js
import { useState, useEffect } from "react"

function useFetch(url) {
  // state 생성
  const [data, setData] = useState(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState(null)

  // hook에서 실행 될 내용 정리
  useEffect(() => {
    async function fetchData() {
      try {
        const response = await fetch(url)
        if (!response.ok) {
          throw new Error("에러가 발생했습니다.")
        }
        const result = await response.json()
        setData(result)
      } catch (error) {
        setError(error)
      } finally {
        setLoading(false)
      }
    }

    fetchData()
  }, [url])

  // state 반환
  return { data, loading, error }
}

export default useFetch
```

실제 컴포넌트에 적용해보면 다음과 같다.

```javascript
function MyComponent() {
  const { data, loading, error } = useFetch("http://test.com")

  return (
    <div>
      {loading ? (
        <div>로딩중입니다.</div>
      ) : error ? (
        <div>에러가 발생했습니다!</div>
      ) : data ? (
        <div>{JSON.stringify(data)}</div>
      ) : null}
    </div>
  )
}

export default MyComponent
```

useFecth 훅을 만들때 return 했던 값들은 사용될 컴포넌트에서 구조분해 할당을 이용해 손쉽게 사용할 수 있다!

## ✅프로젝트 적용 예시

먼저 루트 페이지에 있는 부분부터 코드 최적화를 적용해보고자 했다!
이전 page.tsx는 다음과 같다.

### ➡️기존 코드

```typescript
//page.tsx
"use client"

import HomeTitle from "@/components/HomeTitle"
import { useQuery } from "@tanstack/react-query"
import axios from "axios"
import { useRecoilState } from "recoil"
import { needRefetch } from "@/recoil"
import { useEffect } from "react"
import SK_Home from "./skeleton/SK_Home"
import MusicGrid from "@/components/MusicGrid"
import Preview from "@/components/Preview"

export default function Home() {
  // //recoil
  const [needFetch, setNeedFetch] = useRecoilState(needRefetch)

  const getFirstMusicData = async () => {
    const { data } = await axios.get(
      `/api/searchmusic?pageParam=1&postCount=9&select=all&search=first`
    )
    return data
  }

  const {
    status,
    data: firstMusicData,
    refetch,
  } = useQuery({
    queryKey: ["music-pt", "first"],
    queryFn: getFirstMusicData,
    refetchOnWindowFocus: false,
    refetchOnReconnect: false,
  })

  useEffect(() => {
    if (needFetch) {
      refetch()
      setNeedFetch(false)
    }
  }, [needFetch])

  if (status === "pending") {
    return (
      <main className="flex-1  bg-music-background">
        <div className="container py-20">
          <HomeTitle />
          <SK_Home />
        </div>
      </main>
    )
  }

  return (
    <main className="flex-1 bg-music-background">
      <MusicGrid firstMusicData={firstMusicData} />
    </main>
  )
}
```

### ➡️리팩토링 후

기존에 데이터를 불러오는 부분은 `useFirstMusicData` 훅으로 작성하고, pending 상태에 보여줄 스켈레톤 UI의 경우 리팩토링을 통해 SK_Home 컴포넌트 하나로 처리할 수 있도록 깔끔하게 변경해주었다. 코드가 훨씬 깔끔해지면서 가독성이 좋아진 것이 느껴진다! 😎

```typescript
//page.tsx
"use client"

import SK_Home from "./skeleton/SK_Home"
import MusicGrid from "@/components/MusicGrid"
import useFirstMusicData from "@/hook/useFirstMusicData"

export default function Home() {
  const { status, firstMusicData } = useFirstMusicData()

  if (status === "pending") {
    return <SK_Home />
  }

  return (
    <main className="flex-1 bg-music-background">
      <MusicGrid firstMusicData={firstMusicData} />
    </main>
  )
}
```

**useFirstMusicData** 훅은 아래와 같이 작성했다.

```typescript
//useFirstMusicData.ts
"use client"

import { useEffect } from "react"
import { useQuery } from "@tanstack/react-query"
import axios from "axios"
import { useRecoilState } from "recoil"
import { needRefetch } from "@/recoil"

function useFirstMusicData() {
  // //recoil
  const [needFetch, setNeedFetch] = useRecoilState(needRefetch)

  const getFirstMusicData = async () => {
    const { data } = await axios.get(
      `/api/searchmusic?pageParam=1&postCount=9&select=all&search=first`
    )
    return data
  }

  const {
    status,
    data: firstMusicData,
    refetch,
  } = useQuery({
    queryKey: ["music-pt", "first"],
    queryFn: getFirstMusicData,
    refetchOnWindowFocus: false,
    refetchOnReconnect: false,
  })

  useEffect(() => {
    if (needFetch) {
      refetch()
      setNeedFetch(false)
    }
  }, [needFetch])

  return { status, firstMusicData }
}
export default useFirstMusicData
```

## 📩마무리

`page.tsx` 파일을 우선적으로 커스텀 훅을 만들어 코드 최적화를 진행했는데, 완성된 코드가 정말 깔끔해서 마음에 든다.🤩

앞으로 리팩토링 할 것들이 좀 있지만, 코드는 최대한 가독성과 유지보수성을 향상할 수 있는 방향으로 작성하는게 맞으니, 열심히 리팩토링 해볼 생각이다.

최적화 방식에 대해 학습이 필요한 내용은 무궁무진 하지만 하나씩 배우고 적용해보는거 이렇게 즐거울 줄이야. 또 어떻게 최적화 할지..😎 앞으로도 계속 공부하면서 더 나은 코드를 위해 노력해야겠다.

## 🗂️참고 사이트

- https://qiita.com/as0088to/items/87cf9d0d61e1eb479251
- https://ko.legacy.reactjs.org/docs/hooks-intro.html
- https://ko.react.dev/learn/reusing-logic-with-custom-hooks
