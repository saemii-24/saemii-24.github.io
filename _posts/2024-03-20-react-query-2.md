---
title: "React-Query(TanStack-Query)를 사용해보자! (2) - 무한스크롤"
author: saemii
categories:
  - Library
tags:
  - [Library, React, Next.js]
date: 2024-03-20
last_modified_at: 2024-03-20
pin: true
---

## 📌시작하며

> **무한 스크롤**
> 사용자가 페이지 하단에 도달 할 때 새로운 콘텐츠를 로딩하여 사용자에게 콘텐츠를 끊임없이 제공할 수 있도록 하는 방식

무한 스크롤은 아마 한 번씩은 다들 경험해 본 방식일 것이다.🤔
한 번에 많은 양을 로딩하기에 시간이 오래 걸릴 경우, 분할해서 로딩해 줄 수도 있고, 사용자의 호기심을 자극해 사용자가 더 오래 애플리케이션에 머물 수 있도록 돕는 역할을 하기도 한다.

이번 프로젝트에서 Tanstack Query v5를 통해 이런 무한 스크롤을 구현해서, 해당 내용을 기록할 겸 포스팅을 작성한다.

## ✅전체 구현 흐름

<div class="mermaid" markdown="0" >
graph LR;
B(사용자가 스크롤)--> C(intersectionObserver를 이용해<br/> 일정 위치에 도달하는지 확인);
C-->D(useInfiniteQuery 작동)
</div>

이때 '일정 위치에 도달' 하는 부분은 `intersectionObserver`을 사용해 구현 할 수 있다.

## ✅intersectionObserver

mdn 사이트를 참고하며 이해해보자.

IntersectionObserver는 Intersection Observer API의 인터페이스로, **요소의 가시성 변화를 비동기적으로 감시**하는 도구다.
한 번 생성한 이후에는 설정을 변경할 수 없어서, 이미 생성된 감지기는 지정했던 요소의 가시성 비율의 감지에만 사용할 수 있지만, 하나의 감지기가 다수의 대상을 한꺼번에 주시할 수 있다.

즉, intersectionObserver을 사용해 사용자가 문서 하단에 도달했는지 확인할 수 있다는 것이다!

## ✅react-intersection-observer

라이브러리를 이용하면 훨씬 편하게 구현할 수 있다.

## ✅useInfiniteQuery

```typescript
const {
  fetchNextPage,
  fetchPreviousPage,
  hasNextPage,
  hasPreviousPage,
  isFetchingNextPage,
  isFetchingPreviousPage,
  ...result
} = useInfiniteQuery({
  queryKey,
  queryFn: ({ pageParam }) => fetchPage(pageParam),
  initialPageParam: 1,
  ...options,
  getNextPageParam: (lastPage, allPages, lastPageParam, allPageParams) =>
    lastPage.nextCursor,
  getPreviousPageParam: (firstPage, allPages, firstPageParam, allPageParams) =>
    firstPage.prevCursor,
})
```

전체적인 내용은 이전 포스팅에서 살펴본 useQuery와 비슷하지만, useInfiniteQuery에서 주로 사용되는 옵션들을 한 번 살펴보자.

### ➡️useInfiniteQuery Option

| 옵션                            | 설명                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| queryFn **(필수)**              | 데이터를 요청하는 데 사용하는 함수. `QueryFunctionContext`를 받고, resolve 혹은 error를 throw하는 promise를 반환해야 한다.                                                                                                                                                                                                                                                                          |
| initialPageParam **(필수)**     | 페이지를 가져올 때 사용할 기본 페이지(첫 번째 페이지) 매개변수                                                                                                                                                                                                                                                                                                                                      |
| getNextPageParam **(필수)**     | 새로운 데이터를 받을 때마다 마지막 페이지와 모든 페이지의 전체 배열, 페이지 매개 변수 정보가 전달된다. 다음 페이지의 매개 변수를 반환해야 하며, 다음 페이지가 없는 경우에는 `undefined`, `null`을 반환해 표시할 수 있다.                                                                                                                                                                            |
| getPreviousPageParam **(필수)** | 새로운 데이터를 받을 때마다 첫 번째 페이지와 모든 페이지의 전체 배열, 페이지 매개 변수 정보가 전달된다. 이전 페이지가 없는 경우에는 `undefined`, `null`을 반환해 표시할 수 있다.                                                                                                                                                                                                                    |
| maxPages                        | 무한 쿼리 데이터에 저장할 최대 페이지 수를 뜻한다. 최대 페이지 수에 도달해 새 페이지를 가져오면 지정된 방향에 따라 페이지 배열에서 첫 번째 또는 마지막 페이지가 제거된다. maxPages가 `undefined`이거나 0인 경우에는 페이지 수가 무제한임을 뜻한다. 만약, 최대 페이지 값이 0보다 큰 경우에는 양방향으로 페이지를 가져올 수 있도록 getNextPageParam 및 getPreviousPageParam을 올바르게 정의해야 한다. |

### ➡️useInfiniteQuery Return

| Property               | Description                                                                                                                                                 |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| data.pages             | 모든 페이지를 포함하는 배열이다.                                                                                                                            |
| data.pageParams        | 모든 페이지 매개 변수를 포함하는 배열이다.                                                                                                                  |
| isFetchingNextPage     | fetchNextPage로 다음 페이지를 가져오는 동안 `true`가 된다.                                                                                                  |
| isFetchingPreviousPage | fetchPreviousPage로 이전 페이지를 가져오는 동안 `true`가 된다.                                                                                              |
| fetchNextPage          | 다음 "페이지"의 결과를 가져오며, getNextPageParam과 함께 사용된다.                                                                                          |
| fetchPreviousPage      | 이전 "페이지"의 결과를 가져온다.                                                                                                                            |
| hasNextPage            | 가져올 다음 페이지가 있는 경우 `true`가 된다.                                                                                                               |
| hasPreviousPage        | 가져올 이전 페이지가 있는 경우 `true`가 된다.                                                                                                               |
| isRefetching           | 백그라운드에서 refetch가 진행 중인 경우에 `true`가 된다. (이 상황은 초기 pending 상태나 다음 페이지 또는 이전 페이지의 fetching 경우를 뜻하는 것이 아니다.) |

## ✅프로젝트 내에서 구현 방식

supbase를 이용하고 있기 때문에 기본 틀에서 api 요청 후 supabase에서 일정 갯수의 데이터를 받아온 다음 return 해주는 부분이 추가 되었다.

<div class="mermaid" markdown="0" >
graph LR;
B(사용자가 스크롤)--> C(intersectionObserver를 이용해<br/> 일정 위치에 도달하는지 확인);
C-->D(useInfiniteQuery 작동 / API 요청);
D-->F(supabase에서 값 받아오기);
</div>

## 📩마무리

사실 무한 스크롤 내용 학습 당시에 v4 내용으로 학습했더니(23.09에 촬영된 강의), 실제 사용할 때는 이미 v5로 업데이트(23.10 업데이트) 되어 있었다.😅

아예 처음부터 다시 학습할 내용은 아니었지만, 아무리 최근 강의더라도 기술은 끊임없이 업데이트 된다는 걸 다시 한 번 느끼게 됐던 계기였다.😎

## 🗂️참고 사이트

- <https://tanstack.com/query/latest/docs/framework/react/reference/useInfiniteQuery>
- <https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver>
