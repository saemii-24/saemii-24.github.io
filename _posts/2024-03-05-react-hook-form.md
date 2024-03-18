---
title: "React-Hook-Form(1) - 기본 사용방법 알아보기"
author: saemii
categories:
  - Library
tags:
  - [Library, React, Next.js]
date: 2024-03-05
last_modified_at: 2024-03-05
pin: true
---

## 📌시작하며

이번 프로젝트에는 form을 자주 사용하는데, form을 다룰 때 사용하면 좋은 라이브러리 중 `React-Hook-Form`을 도입해보고자 한다.

## ✅ React-Hook-Form

공식 사이트에서는 이렇게 소개한다!

> React-Hook-Form은 성능이 우수하고 유연하며 쉬운 유효성 검사를 통한 확장 가능한 폼입니다.

그럼 일단 설치하고 시작해보자!😎

```bash
yarn add react-hook-form
```

## ✅ 간단한 예제 살펴보기

아래 예제를 하나씩 살펴보며 각각 무슨 역할을 하는지 알아보자!

```typescript
"use client"
import { useForm } from "react-hook-form"

type FormValues = {
  firstName: string
  lastName: string
}

export default function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
    watch,
  } = useForm<FormValues>({
    defaultValues: {
      firstName: "",
      lastName: "",
    },
  })

  console.log(watch("firstName"))
  let firstName = watch("firstName")
  let lastName = watch("lastName")

  const onSubmit = (data) => {
    console.log(data)
  }

  return (
    <div>
      <form onSubmit={handleSubmit(onSubmit)}>
        <input {...register("firstName", { required: true })} />
        <p>{errors.firstName && "필수값 입니다"}</p>

        <input {...register("lastName", { required: true, minLength: 4 })} />
        <p>{errors.lastName && "필수값 입니다. 2글자 이상 입력해주세요."}</p>

        <p>내 이름은 : {lastName + firstName}</p>

        <button type="submit">등록</button>
      </form>
    </div>
  )
}
```

### ➡️ useForm과 타입지정

next.js에서 사용시 `use client`를 상단에 작성해주고, useForm을 불러온다.

Typescript에서는 아래와 같이 다루게 될 폼의 타입을 지정해 useForm에 적용해주면 된다.

```typescript
"use client"
import { useForm } from "react-hook-form"

type FormValues = {
  firstName: string
  lastName: string
}
```

### ➡️ useForm 사용하기

이제 useForm 안에서 필요한 내용을 구조 분해 해준다. 아래에 사용 된 값들은 다음과 같다.

| useForm의 속성들 | 역할                   |
| ---------------- | ---------------------- |
| register         | input 필드 등록        |
| handleSubmit     | 폼 제출 이벤트 처리    |
| formState        | 폼 상태 관리           |
| watch            | 입력 필드 값 변경 확인 |

여기에 추가적으로, `defaultValues`로 각 input값을의 기본값을 지정해줄 수 있다.

```typescript
export default function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
    watch,
  } = useForm<FormValues>({
    defaultValues: { 👈
      firstName: "",
      lastName: "",
    },
  })

  return(
    //생략
  )
}
```

### ➡️ register

`React-Hook-Form` 기본 정보를 등록하는 과정이다. 아래 예제에서 `"firstName"` 은 name 속성으로 이용되며, 옆의 required는 이 input의 규칙을 지정하는 역할을 한다.

```typescript
<input {...register("firstName", { required: true })} />
```

### ➡️ handleSubmit

form 제출 이벤트를 처리한다. 일반적으로 form 이벤트를 처리할 때 `e.preventDefault()`를 사용하는데, handleSubmit은 기본적으로 이 내용을 포함하기에 따로 적어주지 않아도 된다.

```typescript
const onSubmit = (data) => { 👈
  console.log(data)
}

return (
  <div>
    <form onSubmit={handleSubmit(onSubmit)}👈>
      <input {...register("firstName", { required: true })} />
      <p>{errors.firstName && "필수값 입니다"}</p>

      <button type="submit">등록</button>
    </form>
  </div>
)
```

### ➡️ formState

아래 예제에서 `firstName` input은 `required:true` 조건을 주었다.
만약 사용자가 이 값을 채우지 않고 submit을 시도하면, `<p>{errors.firstName && "필수값 입니다"}</p>` 가 표시되며, 사용자에게 정보를 알릴 수 있다.

즉 formState는 폼 상태관리에 사용되며, 여기에서 `errors`값을 이용해, 유효성검사를 통과하지 못했을 경우, 사용자에게 올바른 값 작성을 유도할 수 있다.

```typescript
<form onSubmit={handleSubmit(onSubmit)}>
  <input {...register("firstName", { required: true })} />
  <p>{errors.firstName && "필수값 입니다"}</p>👈
  <button type="submit">등록</button>
</form>
```

### ➡️ watch

`watch`를 적용하면, 해당 input 값이 변경되는 걸 실시간으로 확인할 수 있다.
변수에 할당하면, input 값이 변경 될때마다 변수에 새로운 값이 할당 된다.

```typescript
let firstName = watch("firstName")
let lastName = watch("lastName")

console.log(watch("firstName"))
```

## ✅ 규칙

React-Hook-Form을 사용해서 아래와 같은 규칙을 쉽게 사용할 수 있다.

| 규칙      | 의미                              |
| --------- | --------------------------------- |
| required  | 필수 입력                         |
| min       | 숫자 필드의 최솟값                |
| max       | 숫자 필드의 최댓값                |
| minLength | 문자 필드의 최소 길이             |
| maxLength | 문자 필드의 최대 길이             |
| pattern   | 정규식 패턴과 일치한지 확인       |
| validate  | 사용자 정의 유효성 검사 함수 지정 |

### ➡️ required

해당 값이 반드시 필수 임을 지정한다.

```typescript
<form onSubmit={handleSubmit}>
  <input {...register("firstName", { required: true })} /> 👈
  <p>{errors.firstName && "필수값 입니다"}</p>
  <button type="submit">등록</button>
</form>
```

### ➡️ minLength

문자 필드의 최소 길이를 결정한다. 예제에서는 2를 주었으므로, 2글자 이상 입력해야 하며 규칙을 만족하지 않을 경우 `필수값 입니다. 2글자 이상 입력해주세요.`가 표시된다.

```typescript
<form onSubmit={handleSubmit}>
  <input {...register("lastName", { required: true, minLength: 2 })} /> 👈
  <p>{errors.lastName && "필수값 입니다. 2글자 이상 입력해주세요."}</p>
  <button type="submit">등록</button>
</form>
```

### ➡️ pattern

정규식 패턴을 지정해 값을 검사할 수 있다.

```typescript
<form onSubmit={handleSubmit}>
  <input
    type="text"
    placeholder="이메일 주소"
    {...register("email", {
      required: true,
      pattern: [원하는 정규식 입력], 👈
    })}
  />
  {errors.email && <p>올바른 이메일 주소를 입력하세요.</p>}
  <button type="submit">등록</button>
</form>
```

### ➡️ validate

사용자 정의 유효성 검사 함수를 만들어 지정할 수 있다. 만약 이미지 파일을 업로드하는데, png,와 jpg로 확장자를 제한하고 싶다면, 아래와 같이 파일을 확인할 수 있다. 이때 return 값은 `<p>{errors.file.message}</p>` 를 표시하는데 사용된다.

참고로 File을 다룰 경우 Type은 `FileList`를 지정해주면 된다.

```typescript
const checkFileType = (value: FileList) => { 👈
  if (value.length === 0) {
    return "파일을 업로드해주세요!"; //errors.file.message 에 출력
  }

  // 확장자 확인
  const correctFormat = ["image/png", "image/jpg"]; //허용 파일 타입
  const file = value[0];
  const fileExtension = file.type.toLowerCase(); //파일 타입

  if (!correctFormat.includes(fileExtension)) {
    return "올바른 파일 형식이 아닙니다!"; //errors.file.message 에 출력
  }

  return true;
};

//생략//
return (
  <form onSubmit={handleSubmit}>
    <input
      type="file"
      {...register("file", {
        required: true,
        validate: checkFileType, 👈
      })}
    />
    {errors.file && <p>{errors.file.message}</p>}
  </form>
)
```

## 📩마무리

form을 다룰 때 자주 사용하는 라이브러리인 만큼 이번 프로젝트에 적용해보면서, 적응해보면 좋을 것 같다.❤️‍🔥

## 🗂️참고 사이트

- <https://react-hook-form.com/docs/useform>
- <https://react-hook-form.com/ts>
