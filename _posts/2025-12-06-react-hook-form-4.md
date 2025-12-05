---
title: "React-Hook-Form(3) - 동적으로 폼이 바뀌어야 하는 경우"
author: saemii
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
