타입 지옥에 빠져 쓰는 글...
예를 들어 하나의 엔드포인트가 있고, 어떤 데이터를 받을지 요청 파라미터로 선택할 수 있는 구조의 query를 작성한다고 해보자.

그럼 당연히 파라미터 값에 따라 response도 달라지게 된다.

이걸 고정 Response type로 설계하는 순간 타입 지옥에 빠지게 된다 _(눈물로 쓰여진 글)_

이를 해결하기 위해서는 `요청 타입 > 응답 타입`을 연결해주는 타입 맵이 필요하다.

```typescript
type FuncNameTypeMap = {
  furniture: Furniture
  mail: Mail
  cutlery: Cutlery
}
```

그 다음, 요청 파라미터를 제네릭으로 설계해준다. 이렇게 작성하면 실제 사용 시 `useFuncName({ types: ['furniture', 'cutlery'] })` 여기서 T는 'furniture' | 'cutlery' 로 추론된다.

```typescript
type FuncNameParams<T extends keyof FuncNameTypeMap> = {
  enabled?: boolean
  types: T[]
}
```

이제 Response Type을 지정해주자.

```typescript
type FuncNameResponse<T extends keyof FuncNameTypeMap> = {
  time: {
    requestTime: number
    responseTime: number
  }
  data: {
    [K in T]: FuncNameTypeMap[K][]
  }
}
```
