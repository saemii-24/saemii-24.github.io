---
title: "React-Hook-Form(3) - 동적으로 폼이 바뀌어야 하는 경우"
author: saemii-24
categories:
  - Library
tags:
  - [Library, React, Next.js]
date: 2024-12-06
last_modified_at: 2024-12-06
pin: true
---

## 💝 정보를 받자, 단 내가 원하는 것 만

현재 제작하고 있는 admin은 프로토타입에 가깝기 떄문에 사용자에게 받아야 하는 정보가 추 후 달라질 가능성이 컸다.
따라서 BE에서 받아야 하는 column값을 받는다면 추 후 FE 수정 없이 바로 변경할 수 있기 떄문에 흐름은

1. BE에 column 값 요청
2. 응답 값 기준으로 FE에서 폼 동적 렌더링
3. 사용자 작성
4. 해당 값을 BE에 전송

다음과 같다. 처음에 이 흐름을 전달 받았을 때는 동적 렌더링이야 너무 자주 해왔던 거라 그러려니 했지만, 예상치 못한 난관에 부딛혔으니...

1. options를 렌더링 해야 하는 dropdown 의 경우 내가 원하는 형태의 데이터가 아니라 가공이 필요했고
2. 내가 계획한 UX에 따라 BE 데이터 형식은 같지만 토글로 렌더링 or 라디오 버튼으로 렌더링 하는 방식이 달라져야 했다
3. 그리고 React Hook Form에 이미 지정된 타입을 동적으로 받을 수 있도록 변경해야 했다.

이러고 보니 기존에 BE 데이터를 받기전 작업했던 컴포넌트 형태를 눈물을 머금고 래팩토링 해야 하는 상황이었던 것이다. (...)

그래서 오늘은 저 3번 React Hook Form에게 이 폼은 동적 렌더링이 될 거야~ 라는 걸 어떻게 전달해서 작업할지를 정리해보겠다..

## 🫡 데이터 살펴보기

아래는 내가 필요한 데이터를 기반으로 만들어 본 간략한 데이터다!

comp값을 기준으로 미리 만들어 둔 컴포넌트를 렌더링 하면 된다.

~~실제로 받았던 데이터는 내가 상상했던 데이터와 너무 달라서 애먹었다~~

```javascript
const formData = [
  {
    name: "fruit",
    comp: "radio",
    label: "좋아하는 과일",
    defaultValue: "",
    rules: { required: "과일을 선택해주세요." },
    options: [
      { value: "banana", label: "바나나" },
      { value: "apple", label: "사과" },
    ],
  },
  {
    name: "agree",
    comp: "checkbox",
    label: "약관에 동의합니다",
    defaultValue: false,
    rules: { required: "필수 동의 항목입니다." },
  },
  {
    name: "city",
    comp: "select",
    label: "거주 도시",
    defaultValue: "",
    rules: { required: "도시를 선택해주세요." },
    options: [
      { value: "seoul", label: "서울" },
      { value: "busan", label: "부산" },
    ],
  },
]
```

## 🍎 react hook form에 관리가 필요한 내용 전달하기

### ⭐ 폼 타입 지정

일단 타입을 지정하자. 타입을 어떻게 지정하면 좋을까.. 생각했는데 생각보다 간단하다.

```typescript
type DefaultValues = {
  [key: string]: any
}
```

### ⭐ Default Value 지정

일단 어떤 값이 들어올지 모르니, key는 string으로, value는 any를 주었다. 물론 value값이 지정되어 있다면 지정해주는 것이 좋다.

만약 값이 지정되어야 하는 경우라면 미리 formData(위에서 지정한 mockData)에서 받은 값을 이용해 다음과 같이 진행해주면 된다.

```typescript
const defaultValues = formData.reduce<DefaultValues>((acc, cur) => {
  acc[cur.name] = cur.defaultValue ?? ""
  return acc
}, {})
```

### ⭐ Rendering 하기

이제 준비는 끝났다! 미리 지정해 둔 comp를 바탕으로 렌더링 해보자.

```typescript
const FieldRegistry: any = {
  radio: RadioField,
  checkbox: CheckboxField,
  select: SelectField,
}

function FormField({ field, control }: any) {
  const Component = FieldRegistry[field.comp]
  if (!Component) return null

  return <Component field={field} control={control} />
}
```

FieldRegistry는 컴포넌트를 값으로 가지는 객체로, formData의 comp 타입에 따라 어떤 컴포넌트를 렌더링 할지 결정하는 맵 역할을 한다.

```typescript
const FieldRegistry = {
  radio: RadioField,
  checkbox: CheckboxField,
  select: SelectField,
}
```

이를 바탕으로 FormField 컴포넌트에서 직접 컴포넌트를 렌더링 하게 된다.
FormField를 실제로 사용하는 방식을 살펴보면 다음과 같다.

```typescript
export default function RenderFormPage() {
  const { control, handleSubmit } = useForm({
    defaultValues,
  })

  const onSubmit = (data: any) => console.log(data)

  return (
    <div className="flex h-screen w-screen flex-col items-center justify-center gap-10">
      <h2 className="text-2xl font-bold">동적으로 폼 렌더링 하기</h2>

      <form onSubmit={handleSubmit(onSubmit)} className="flex flex-col gap-8">
        {formData.map((data) => (
          <FormField key={data.name} field={data} control={control} />
        ))}

        <button
          type="submit"
          className="rounded bg-blue-500 px-4 py-2 text-white"
        >
          제출하기
        </button>
      </form>
    </div>
  )
}
```

## 🤨 잠깐, RHF의 control과 Controller의 역할이 뭘까?

여기서 control을 내려 사용하는데 주로 register를 사용했기 때문에 이게 정확히 어떤 역할을 하는지 헷갈렸다.

일단 기본적으로 RHF는 register 된 input을 제어할 수 있다. 하지만 RHF가 직접 컨트롤 할 수 없는 제어 컴포넌트 또한 분명히 존재한다. 이 때 필요한 것이 control + Controller 조합이다.

### 🍊control과 Controller

- control
  control은 RHF의 상태 관리 객체로 폼 필드 값을 저장하고, dirty, touched, errors와 같은 상태를 보관한다.

- Controller
  Controller는 RHF가 직접 조작할 수 없는 컴포넌트를 RHF가 관리할 수 있도록 하는 어댑터 역할을 한다.

  ```typescript
  <Controller
    control={control}
    name="fruits"
    render={({ field }) => <Select {...field} options={fruitsOptions} />}
  />
  ```

  Controller를 작성하고 render 안에 필요한 form 관련 컴포넌트를 작성하는데 이때 field를 함께 전달해야 한다.
  field가 가지고 있는 값은 다음과 같다.

- value
- onChange
- onBlur
- ref

즉 register가 하던 역할을 Controller를 통해 우리가 직접 수동 연결하게 되는 것이다.
기본적으로는 register를 사용하는 것이 더 가볍다고 알려져 있으나, 라이브러리 등을 사용하면서 register로 접근이 불가능 할 때 Controller를 이용해 RHF을 계속 사용할 수 있다. 👍

- <https://react-hook-form.com/docs/useform/control>
- <https://react-hook-form.com/docs/usecontroller/controller>
