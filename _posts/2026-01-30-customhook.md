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

쉽게 url의 parameter를 set하고 get 하는 커스텀 훅으로, `router.push`를 통해 히스토리를 남긴다.

스텝이 여러개이며, 뒤로가기 할 때 해당 데이터를 유지해야 하는 경우에 주로 사용했다.

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
