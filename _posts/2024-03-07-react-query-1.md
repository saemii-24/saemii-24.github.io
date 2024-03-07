---
title: "React-Query(TanStack-Query)를 사용해보자! (1)"
author: saemii
categories:
  - Library
tags:
  - [Library, React, Next.js]
date: 2024-03-07
last_modified_at: 2024-03-07
pin: true
---

## 📌시작하며

리액트 쿼리는 아마 한 번쯤은 다들 들어봤을 거라 생각한다. 따로 강의를 들으면서 리액트 쿼리를 사용해봤는데, 직접 프로젝트에 적용해보는 것은 처음이라, 이번 글에서는 "그래서 리액트 쿼리가 뭔데?!" 라는 질문에 대한 답을 찾아보고, 사용법을 익혀보고자 한다.😄 (이 글은 v5 docs를 참고해 작성했다.)

## ✅React-Query(TanStack Query)

구 리액트 쿼리, 현 탠스택 쿼리라고 불리는 이 라이브러리는 어디에 사용될까? 먼저 공식 문서에서 설명을 살펴보자.

> "TS/JS, React, Solid, Vue, Svelte 및 Angular를 위한 강력한 비동기 상태 관리"
> 세분화된 상태 관리, 수동 리페칭, 구조가 복잡한 비동기 코드는 이제 그만하세요. TanStack Query는 선언적이고, 항상 최신 상태를 유지하는 자동 관리 쿼리와 mutaition을 제공해 개발자와 사용자 경험을 직접적으로 개선합니다.

이 것만 봐서는 이해가 가지 않을 수 있는데, 왜 TanStack Query가 탄생했는지 부터 살펴보자.

## ✅TanStack Query의 탄생

왜 [TanStack Query](https://tanstack.com/query/latest/docs/framework/react/overview)가 탄생했는지는 공식 문서에도 설명하고 있는 부분이니, 문서를 참고해 이해해보자.

대부분의 핵심적인 웹 프레임워크들을 사용한다고 생각해보자. 각 프레임워크에서 데이터를 가져오거나 업데이트하는 방식이 다르다! 이 때문에 개발자는 데이터를 가져오기 위한 자체적인 방법을 개발해야 하는 어려움에 처하곤 한다.🤔

또한 대부분의 상태 관리 라이브러리는 클라이언트측 작업에 적합하지, 비동기 또는 서버 상태 작업에는 적합하지 않다.

예를들어 서버 작업에서는 아래와 같은 내용이 포함 된다.

1. 캐싱
2. 동일한 데이터에 대해 중복 요청이 발생 했을 시 중복 제거
3. 오래된 데이터를 백그라운드에서 업데이트
4. 데이터가 오래되었는지 확인
5. 데이터 업데이트를 가능한 빠르게 반영
6. 페이지네이션, lazy Loading등 성능 최적화
7. 서버 상태 메모리 관리 및 가비지 컬렉션
8. 구조적 공유를 통한 쿼리 결과 메모화

이러한 내용을 처리하는 것이 쉽지 않기 때문에, TanStack Query를 이용해 **서버 상태를 효율적으로 관리**하는 것이다!

## ✅설치

그럼 설치하고 시작해보자!

```bash
$ npm i @tanstack/react-query
# or
$ pnpm add @tanstack/react-query
# or
$ yarn add @tanstack/react-query
# or
$ bun add @tanstack/react-query
```

## ✅예제 살펴보기

Quick Start에서 제공하는 예제를 살펴보고, 어떤 내용이 있는지 하나씩 살펴보자.

```typescript
import {
  useQuery,
  useMutation,
  useQueryClient,
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query"
import { getTodos, postTodo } from "../my-api"

// 클라이언트 만들기
const queryClient = new QueryClient()

function App() {
  return (
    // App에 client를 제공한다.
    <QueryClientProvider client={queryClient}>
      <Todos />
    </QueryClientProvider>
  )
}

function Todos() {
  // Client에 접근한다.
  const queryClient = useQueryClient()

  // 쿼리
  const query = useQuery({ queryKey: ["todos"], queryFn: getTodos })

  // Mutations
  const mutation = useMutation({
    mutationFn: postTodo,
    onSuccess: () => {
      //무효화 및 다시 가져오기
      queryClient.invalidateQueries({ queryKey: ["todos"] })
    },
  })

  return (
    <div>
      <ul>
        {query.data?.map((todo) => (
          <li key={todo.id}>{todo.title}</li>
        ))}
      </ul>

      <button
        onClick={() => {
          mutation.mutate({
            id: Date.now(),
            title: "Do Laundry",
          })
        }}
      >
        Add Todo
      </button>
    </div>
  )
}

render(<App />, document.getElementById("root"))
```

## ✅QueryClient

QueryClient는 애플리케이션의 전체 상태, 퀄리 결과를 관리하고, 캐시와 상호 작용하는 데 사용된다.

## ✅QueryClientProvider

QueryClientProvider를 사용해 애플리케이션에 QueryClient를 연결한다.

```typescript
import { QueryClient, QueryClientProvider } from "@tanstack/react-query"
import { getTodos, postTodo } from "../my-api"

// 클라이언트 만들기
const queryClient = new QueryClient()

function App() {
  return (
    // App에 client를 제공한다.
    <QueryClientProvider client={queryClient}>
      <Todos />
    </QueryClientProvider>
  )
}
```

## ✅useQuery

useQuery 함수를 사용해 데이터 불러오기, 자동 캐싱, 중복 요청 방지가 가능하다.

useQuery를 통해 데이터를 가져오면, 해당 데이터가 캐시되고, 동일한 쿼리가 호출 될 경우 네트워크 요청을 보내지않고 캐시 된 데이터를 사용해 중복 요청이 방지되므로, 성능최적화에도 도움이 된다.

### ➡️Query

추가로, 여기서 말하는 쿼리가 무엇인지 살펴보자!
쿼리는 queryKey라는 고유한 key에 연결된 비동기 데이터 소스를 말한다.

따라서, 쿼리는 서버에서 데이터를 가져오기 위해 Promise 기반의 모든 방법(포함되어 있는 GET 및 POST 방법)과 함께 사용할 수 있다. 이때 서버에서 데이터를 수정해야 할 경우에는, 뮤테이션을 사용하는 것을 권장하고 있다.

### ➡️useQuery 예제

```typescript
function Todos() {
  const { status, data, error } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodoList,
  })

  if (status === "pending") {
    return <span>Loading...</span>
  }

  if (status === "error") {
    return <span>Error: {error.message}</span>
  }

  return (
    <ul>
      {data.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  )
}
```

#### 💟 queryKey

먼저 위에서 말한 것 처럼, 쿼리는 `queryKey`라는 고유한 key에 연결된 비동기 데이터 소스이므로, 고유한 `queryKey`를 작성해주어야 한다.
이 Key값은 애플리케이션 전체에서 쿼리를 다시 가져오고, 캐싱하고, 공유하는 데 내부적으로 사용된다.

```typescript
const { status, data, error } = useQuery({
  queryKey: ["todos"],
  queryFn: fetchTodoList,
})
```

#### 💟queryFn

queryFn은 쿼리 실행을 위한 함수다! 쿼리를 실행하면 비동기 작업을 수행한다. 즉, 예제의 `fetchTodoList`는 비동기 작업을 위한 함수가 작성되어 있을 것으로 예상할 수 있다.

```typescript
async function fetchTodoList() {
  try {
    const response = await fetch("/api/todos")
    const data = await response.json() //json 파싱
    return data
  } catch (err) {
    throw new Error(`error가 발생했습니다: ${err.message}`)
  }
}
```

#### 💟status

비동기 작업 중, 사용성을 위해 데이터를 가지고 올 때는 Loading을 보여주고, 에러가 발생할 경우 에러가 발생함을 적극적으로 알릴 필요가 있다.😎

이를 쿼리 실행 후 결과값을 이용해 간단히 구현할 수 있다.
각 상황별로 **렌더링할 값들을 정해줄 수 있기 때문**이다!

그럼 이제, 쿼리의 실행 값들을 어떻게 사용할지 살펴보자. 두 가지 방법으로 현재 쿼리 실행 후 결과값을 확인할 수 있다.

직접적으로 `isPending`, `isError`의 boolean 값을 이용하는 경우

```typescript
const { isPending, isError, data, error } = useQuery({
  queryKey: ["todos"],
  queryFn: fetchTodoList,
})

if (isPending) {
  return <span>Loading...</span>
}

if (isError) {
  return <span>Error: {error.message}</span>
}
```

`status`, `isError`의 값을 이용하는 경우

```typescript
const { status, data, error } = useQuery({
  queryKey: ["todos"],
  queryFn: fetchTodoList,
})

if (status === "pending") {
  return <span>Loading...</span>
}

if (status === "error") {
  return <span>Error: {error.message}</span>
}
```

즉, 여기서 `status === "pending"` 인 경우, `return <span>Loading...</span>` 값을 사용자에게 보여주어, 현재 데이터를 받아오는 작업을 시행 중이라는 걸 사용자에게 알릴 수 있다!

각 내용이 담고 있는 의미는 다음과 같다.
|상태|의미|
|---|---|
|isPending, status==='pending'| 쿼리에 아직 데이터 없음|
|isError, status==='error'| 쿼리에 오류 발생|
|isStatus, status==='success'| 쿼리 성공, 데이터 사용 가능|

## ✅useMutation

이번엔 useMutation을 사용해보자!

### ➡️Mutations

mutations이 무엇일까? mutations는 쿼리와 달리 일반적으로 데이터를 생성/업데이트/삭제하거나 server side effect를 수행하는 데 사용된다!

#### 💟mutationFn

mutationFn은 비동기 작업을 수행한다. 아래 예제는 데이터 수정을 위해 addTodo 비동기 작업을 수행하고, mutation 작업의 성공, 완료 여부에 따라 실행하는 예제이다.

```typescript
useMutation({
  mutationFn: addTodo,
  onSuccess: async () => {
    console.log("I'm first!")
  },
  onSettled: async () => {
    console.log("I'm second!")
  },
})
```

#### 💟options

mutation은 특정 순간에 아래 중 하나의 상태를 띄게 된다.
| 상태 | 설명 |
|-----------|--------------------------------------------|
| isIdle or status === 'idle' | 뮤테이션이 현재 유휴 상태이거나 초기화된 상태 |
| isPending or status === 'pending' | 뮤테이션이 현재 실행 중 |
| isError or status === 'error' | 뮤테이션이 오류를 만났을 때 |
| isSuccess or status === 'success' | 뮤테이션이 성공적으로 완료되어 데이터 사용 가능 |

## ✅invalidateQueries

사용자의 행동에 따라 데이터가 변동되어, 쿼리를 다시 가져와야할 때 사용할 수 있는 메소드다.

```typescript
const toggleLike = useMutation(toggleLikeFunc, {
  onSuccess: () => {
    // 좋아요가 수정되면 기존의 쿼리를 무효화하고 다시 불러오기
    queryClient.invalidateQueries(likeQuery)
  },
})
```

## 📩마무리

아직 이 포스트만으로는 완전히 이해하지 못해서🥲 일단 사용해보면서 내용을 추가해야 할 것 같다..!

## 🗂️참고 사이트

- <https://tanstack.com/query/latest/docs/framework/react/overview>
