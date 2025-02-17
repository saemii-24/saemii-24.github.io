---

title: "에러 핸들링 - fetch 중 발생하는 에러"
author: saemii
categories: 
tags: - [Next.js, Error]
date: 2025-02-16
last_modified_at: 2025-02-16
pin: true

---

## 📌시작하며

계속해서 fetch 도중 발생하는 에러 handling에 대해 정리해보자.

## ✅api

fetch 중 문제가 발생하는 상황을 가정하기 위해, next.js에서 api를 하나 만들자.
아래 예제는 해당 경로로 POST 요청을 보냈을 시에, 404를 return 한다.

```typescript
// 경로는 /api/error
import { NextResponse } from "next/server"
import path from "path"

export async function POST(request: Request) {
  //에러 테스트
  return NextResponse.json(
    { message: "통신 도중 에러가 발생했습니다." },
    { status: 400 }
  )
}
```

## ✅fetch api 사용하기

앞선 포스팅에서 next.js가 에러를 인식하려면 생명주기 중에 에러가 발생해야 한다고 했는데, 이를 염두해 두고 예제를 작성해보았다.

버튼을 클릭하면, 위에서 작성한 api 엔드포인드에 요청을 하게 되는데, 해당 엔드포인드는 404를 return 하므로 `if (!response.ok)` 안에서 `throw new Error("에러가 발생했습니다")`가 실행되어 catch 문으로 넘어가게 된다.

이때 발생한 에러(err)은 Error 객체이므로, setError(err)이 실행되고, useEffect도 error가 변화함에 따라 함께 실행되어 리액트 생명주기 안에서 fetch 중 발생한 에러가 관리된다.

결과적으로, error.tsx가 정상적으로 동작하게 된다.

```typescript
"use client"
import { useEffect, useState } from "react"

export default function FetchErrorExample() {
  const [error, setError] = useState<Error | null>(null)

  const fetchError = async () => {
    try {
      const response = await fetch("/api/error", {
        method: "POST",
      })

      if (!response.ok) {
        throw new Error("에러가 발생했습니다")
      }
    } catch (err) {
      // instance of = 객체가 특정 클래스의 인스턴스인지 확인한다.
      // 즉, err가 ERrror 객체인지 확인하는 과정이다.
      if (err instanceof Error) {
        setError(err)
      }
    }
  }

  useEffect(() => {
    if (error) throw error // 렌더링 과정에서 에러 발생
  }, [error])

  return (
    <button
      className="bg-pink-500 cursor-pointer hover:bg-pink-500/70 text-white p-2 rounded-md"
      onClick={fetchError}
    >
      fetch 에러 발생
    </button>
  )
}
```

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/building-your-application/routing/error-handling>
