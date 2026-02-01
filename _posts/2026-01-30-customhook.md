---
title: "자주 쓰는 custom hook 정리"
author: saemii-24
categories:
  - Next.js
tags:
  - [Next.js]
date: 2025-12-22
last_modified_at: 2025-12-22
pin: true
---

내가 자주 사용하는 custom hook을 정리해보자.

## ⭐ useParameter

단계별로 테이브을 다루고 있는데, 이 때 사용자 편의성을 위해 히스토리를 유지하면서 상태를 반영해야 하는 경우가 자주 발생한다.

이를 위해 아래와 같은 커스텀 훅을 만들었다.

```typescript
"use client"

import { useRouter, useSearchParams } from "next/navigation"

export function useParameter() {
  const router = useRouter()
  const searchParams = useSearchParams()

  // GET
  const getParameter = (key: string): string | null => {
    return searchParams.get(key)
  }

  // SET
  const setParameter = (key: string, value: string | null) => {
    const params = new URLSearchParams(searchParams.toString())

    if (value === null) {
      params.delete(key)
    } else {
      params.set(key, value)
    }

    router.push(`?${params.toString()}`)
  }

  return {
    getParameter,
    setParameter,
  }
}
```

### ⚙️ 훅의 동작 방식

- **getParameter**

1. 현재 URL의 query string에서 특정 key의 값을 조회한다.
2. 값이 없을 경우 null을 반환한다.

- **setParameter**

1. 기존 query parameter를 유지한 채, 특정 key의 값을 추가·수정·삭제한다.
2. router.push를 사용하기 때문에 브라우저 히스토리가 남는다.
3. value가 null인 경우 해당 parameter를 URL에서 제거한다.
