---
title: "Typescript(2)"
author: saemii
categories:
  - Typescript
tags:
  - [Typescript, Language]
date: 2024-03-07
last_modified_at: 2024-03-07
pin: true
---

## 📌시작하며

이전 포스트에서 타입스크립트의 기본적인 내용을 살펴봤다면, 이번엔 타입스크립트를 편하게 사용하게 해주는 제네릭과 제네릭 유틸리티 타입에 대해 알아보고자 한다.😎

> 이 글은 아래와 같이 이어집니다.
>
> - [Typescript(1)](https://saemii-24.github.io/posts/typescript-1/)
> - [Typescript(2)](https://saemii-24.github.io/posts/typescript-2/)

## ✅제네릭

### ➡️기존 사용 방식

제네릭은 코드를 더 유연하게 만드는 기능을 한다.

기존에 타입스크립트를 작성할 때와 다르게 처음부터 구체적인 타입을 지정하는 것이 아니라 **사용시에 동적으로 타입을 할당**할 수 있는 것이다!

예를 들어, 아래와 같은 코드가 있다고 가정하자.
그럼, 매개변수가 number고, return 값도 number 임을 알 수 있다. 이미 처음부터 그렇게 설정해 두었기 때문이다.

```typescript
function identity(arg: number): number {
  return arg
}
```

만약 매개변수의 값이 어떤 타입인지 알 수 없다면 어떻게 할까? `any`를 써서 다음과 같이 작성하는 방법도 있다. 하지만 문제는 return 값도 `any`이기 때문에 `function identity(arg: any): any` 부분만 본다면 어떤 타입의 값이 나오는지 알 수 없다.

```typescript
function identity(arg: any): any {
  return arg
}
```

### ➡️제네릭 사용하기

#### 💟선언

이럴때 제네릭이 빛을 발한다!✨ 예제를 살펴보자!
먼저, 아래 코드에서 `<T>`는 제네릭 타입 매개변수를 말한다. 물론, 다른 이름을 사용해도 된다. ex) `<Type>`등

먼저 해당 코드에서 제네릭을 사용하겠다고 `<T>`를 붙여 알려준다. 그다음 arg 매개변수에 해당 제네릭 타입을 선언하고, 반환 값도 동일하게 선언해주었다.

```typescript
function identity<T>(arg: T): T {
  return arg
}
```

#### 💟호출

사용할 땐 다음과 같이 사용한다.
함수를 호출하면서 `<T>`에 들어갈 타입을 넣어 전달해주면 된다. <u>물론, 이 경우에도 타입 추론을 진행하므로 명시적으로 적지 않아도 작동한다.</u>

아래 예제들은 모두 에러가 발생하지 않는 코드다.

매개변수 타입이 다양한 함수를 모두 작성할 필요없이 하나의 함수를 선언하고, 아래와 같이 호출해주면 되니 코드가 훨씬 깔끔해진다.

```typescript
let result1: number = identity<number>(2)
let result2: string = identity<string>("Hi!")
let result3: number[] = identity<number[]>([1, 2, 3])
```

매개변수에 `<T>`를 그대로 사용하지 않고, 아래와 같이 사용할 수 있다.

```typescript
function loggingIdentity<T>(arg: T[]): T[] {
  console.log(arg.length) // arg는 T타입을 가지고 있는 배열이므로,length를 가지고 있다. 즉, 오류가 발생하지 않는다.
  return arg
}
```

### ➡️제네릭 제약조건

제네릭을 통해 유연하게 작성할 수 있다는 장점이 있지만, 제네릭에 사용하는 타입을 좁혀서 사용하고 싶을 수 있다. 이때 사용하는 것이 **제약조건** 이다.

먼저 첫번째 `User`타입을 보자. etc에는 어떤 타입이든 들어올 수 있다.

```typescript
interface User<T> {
  name: string
  etc: T
}
```

이때 저 etc에 string이나 number값만 들어오게 제약을 걸어보자!
제약을 걸 때는 `extends`키워드를 사용해 작성한다.

```typescript
//제약을 건다면?
interface UserConstraints<T extends string | number> {
  name: string
  etc: T
}

//이렇게 작성도 가능!
interface Weight {
  weight: number
}

interface Height {
  height: number
}

interface UserConstraints<T extends Weight | Height> {
  name: string
  etc: T // {weight: 70} 혹은 {height: 160} 속성을 가질 수 있다.
}
```

## ✅제네릭 유틸리티 타입

제네릭 유틸리티타입이란 타입스크립트에서 제네릭 타입을 유연하게 다룰 수 있도록 만들어 준다! 자주 사용하는 것 위주로 살펴보자😎

### ➡️Partial&lt;T&gt;

제네릭 타입의 **모든 속성을 선택적**으로 만든다! 아래 코드는 User type이 Partial에 의해 모두 선택 속성이 되어, 모두오류가 발생하지 않는다!

```typescript
interface User {
  name: string
  age: number
}

const shin: Partial<User> = { name: "jjang-gu" } ⭕
const han: Partial<User> = { age: 5 } ⭕
const lee: Partial<User> = {} ⭕
```

### ➡️Pick&lt;T&gt;

**일부 속성만 선택**해 새 타입을 생성한다! 아래 예제를 살펴보자. User type과 기존의 User type에서 name과 age 속성만 빼서 UserInfo type을 새로 만들었다. 즉 아래와 같은 결과를 예측할 수 있다.

```typescript
interface User {
  name: string
  age: number
  isMarried: boolean
}

type UserInfo = Pick<User, "name" | "age">

const shin:User = { ⭕
  name: "신짱구",
  age: 5,
  isMarried: false
}
const shin:UserInfo = { ❌
  name: "신짱구",
  age: 5,
  isMarried: false //Userinfo 타입에서는 isMarried 속성이 없다.
}
const shin:UserInfo = { ⭕
  name: "신짱구",
  age: 5
}
```

### ➡️Omit&lt;T, K&gt;

Pick이 필요한 것만 뽑아서 새 속성을 만드는 거라면, Omit은 **특정 속성을 제외하고 새로운 속성**을 만든다! 예제를 살펴보자!

```typescript
interface User {
  name: string
  age: number
  isMarried: boolean
}

type WithoutAge = Omit<User, "age">

const shin:User = { ⭕
  name: "신짱구",
  age: 5,
  isMarried: false
}
const shin:WithoutAge = { ❌
  name: "신짱구",
  age: 5, //WithoutAge 타입에서는 age 속성을 삭제했다!
  isMarried: false
}
const shin:WithoutAge = { ⭕
  name: "신짱구",
  isMarried: false
}
```

### ➡️Record&lt;K, T&gt;

왜 K, T로 작성할까? 했는데 K = key이고 T = type이라 이렇게 작성하는 경우가 많은 것 같다.

예를들어 record를 만들때 key값을 string, value 값을 number로 지정하고자 하면 다음과 같이 만들 수 있다!

```typescript
type MyRecord = Record<string, number>

const myRecord: MyRecord = {
  one: 1,
  two: 2,
  three: 3,
}
```

만약 key 값을 확실하게 지정하고 싶다면 아래와 같이 작성할 수 있다.

```typescript
type MyKey = "짱구" | "유리" | "맹구"

const memberAge: Record<MyKey, number> = {
  짱구: 5,
  유리: 4,
  맹구: 6,
}
```

### ➡️Readonly&lt;T&gt;

Readonly는 제네릭 모든 타입을 **읽기 속성**으로 만든다! 쉽게 말해 변수, 속성을 읽기전용으로 만들고 변경을 막는 것이다.

```typescript
interface User {
   name: string
   age: number
}

const user1:User = { name: "짱구", age: 5 }
user1.age = 5 ⭕//재 할당 문제 없음

const user2:Readonly<User> = { name: "짱구", age: 5 }
user2.age = 5 ❌//읽기 전용이므로 재 할당 에러 발생
```

## 📩마무리

이렇게 정리하고 나니 제네릭 사용법을 더 잘 이해할 수 있었다.😎 이제 프로젝트 할 때 좀 더 편하고 다양한 방식으로 타입스크립트를 활용해보자!

## 🗂️참고 사이트

- <https://www.typescriptlang.org/ko/docs/handbook/2/generics.html>
