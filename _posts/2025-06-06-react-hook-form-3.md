---
title: "React-Hook-Form(3) - 입력해야 하는 값을 배열로 받아야 하는 경우"
author: saemii
categories:
  - Library
tags:
  - [Library, React, Next.js]
date: 2024-06-06
last_modified_at: 2024-06-06
pin: true
---

# 📌 프로젝트를 이어받다.

같은 FE 동료의 퇴사가 결정되어, 앞으로 이어서 내가 해당 프로젝트를 작업하게 되었다. 👀

데일리 스크럼을 진행한 덕분에 해당 개발자 분께서 어떤 코드를 작성하실 때 가장 많은 시간을 소요하셨는지, 어떤 것을 중점적으로 생각하고 계신지는 대략적으로 파악하고 있었고, 인수인계 문서를 꼼꼼히 작성해두셔서, 순조롭게 인수인계가 진행중이다.

그러던 중 며칠 전 새로운 요구사항 요청이 들어와 화면안을 받게 되었다.

기존과 달라진 점이라면, 기존에는 react-hook-form을 이용해 input 1개로 데이터 1개를 입력받고 있었는데, 이번엔 input 2묶음을 사용자가 계속 추가하며 입력할 수 있는 형태가 된 것이다.🤔

일단 기존에 Input 컴포넌트가 있었으나, 기능/디자인상 새로 컴포넌트를 만들어야 할 필요성이 느껴졌고, react-hook-form을 통해 한 묶음의 데이터를 어떻게 배열로 관리할 수 있을지가 관건이 되었다.

## 📁 useFieldArray

`useFieldArray`는 `react-hook-form`에서 배열 형태의 입력 field를 제어하기 위해 제공하는 훅으로, 이를 이용해 **사용자가 입력 항목을 추가하거나 제거할 수 있는** 상황을 쉽게 제어할 수 있다.

### 😊 공식문서 예제

`useFieldArray`는 기존에 react-hook-form을 사용하는 것과 동일하게 `useForm()`을 사용해 이 입력 field를 react-hook-form이 관리할 것이다 라고 알려주는 `register` 를 사용하는 것을 동일하다.

이때 게임 확장팩처럼 `useFieldArray`를 함께 사용해준다고 이해하면 된다.

```typescript
function FieldArray() {
  const { control, register } = useForm();
  const { fields, append, prepend, remove, swap, move, insert } = useFieldArray({
    control, // control은 useForm에서 전달받은 것으로, FormProvider를 사용하는 경우 생략 가능
    name: "test", // Field Array를 위한 고유한 이름
  });

  return (
    {fields.map((field, index) => (
      <input
        key={field.id} // key에는 반드시 field의 id를 사용해야 함 (리렌더링 문제 방지)
        {...register(`test.${index}.value`)}
      />
    ))}
  );
}
```

## 🗂️참고 사이트

- <https://react-hook-form.com/docs/usefieldarray>
