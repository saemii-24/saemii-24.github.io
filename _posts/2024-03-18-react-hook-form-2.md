---
title: "React-Hook-Form(2) - reset을 이용한 defaultValue 설정"
author: saemii
categories:
  - Library
tags:
  - [Library, React, Next.js]
date: 2024-03-18
last_modified_at: 2024-03-18
pin: true
---

## 📌시작하며

사용자가 입력한 내용을 수정할 수 있는 페이지를 만들다가, 문제점을 발견했다.
먼저 계획한 로직은 다음과 같다.

<div class="mermaid" markdown="0" >
graph LR;
B(api요청 후<br/>데이터 받아오기)--> C(데이터 줄바꿈 기준으로 split 후<br/>input에 defaultValue 지정);
C--> D(사용자가 그 중 원하는<br/> 값만 수정 후 제출)
</div>

<br/>defaultValue까지는 제대로 지정이 되는데, 사용자가 '원하는 값만 수정 후' 제출 시, 이전에 작성한 값을 그대로 사용하고 싶어, 손대지 않은(커서로 지정하지 않은) input 값은 null 값으로 지정되었다.😅

해당 input 값들은 required로 제한이 되어있기에, submit이 되지 않고 모든 input에 focus를 준 다음에야 제대로 제출 되었다.

input 값이 하나라면 큰 문제가 되지 않지만, map을 이용해 많은 input 값을 만들고 있어, 그 중 딱 하나의 input 값만 교체해야 할 경우 사용성을 크게 저하시키는 문제였다.🤔

## ❌1차 시도

defaultValue 값이 문제일까 싶어, value 값으로 변경해서 시도했으나, 이 또한 동일한 문제가 발생했다. 여전히 모든 input에 focus가 되어야 제출되었다.

```typescript
//기존
<input defaultValue={defaultValue} />

//1차 시도
<input value={defaultValue} />
```

## ❌2차 시도

react-hook-form공식 문서에서 제시하는 `defaultValues`를 사용하는 방식을 시도했으나,
이 또한 동일한 문제가 발생했다.🥲

시도한 로직은 다음과 같다.

<div class="mermaid" markdown="0" >
graph LR;
B(데이터 split해 array로 만들기)--> C(array을 react-hook-form의 defaultValues에<br/> 등록할 수 있는 형태의 객체로 제작);
C--> D(setState로 만든 객체 값 지정,<br/> defaultValues에 적용)
</div>

```typescript
//defaultValue를 만들기 위한 state
let [defaultFormValue, setDefaultFormValue] = useState<{
  [key: string]: string
}>({})

//arr을 받아 {register에 등록 된 이름: value} 형식의 객체 만들기
function makeDefaultObj(arr: string[]) {
  let defaultValueObj: { [key: string]: string } = {}
  arr.forEach(
    (item: string, index: number) => (defaultValueObj[`lyrics_${index}`] = item)
  )
  return defaultValueObj
}

useEffect(() => {
  let translatedText = ""

  if (lang === "ko" && music.kotranslate) {
    translatedText = music.kotranslate
  } else if (lang === "jp" && music.jptranslate) {
    translatedText = music.jptranslate
  }

  if (translatedText) {
    const splitTranslate = translatedText.split("\n")
    setDefaultTranslate(splitTranslate)
    const defaultObj = makeDefaultObj(splitTranslate)
    setDefaultFormValue(defaultObj) //react-hook-form의 기본값 설정
  }
}, [music, lang])

//react-hook-form
const {
  register,
  handleSubmit,
  formState: { errors },
} = useForm({
  defaultValues: defaultFormValue, 👈
})
```

## ⭕해결

최종적으로 해결 방법은 `reset`을 사용하는 것이었다. 전체적인 로직은 2차 시도와 동일하고, `reset`을 추가하는 것이었다.

```typescript
useEffect(() => {
  let translatedText = ""

  if (lang === "ko" && music.kotranslate) {
    translatedText = music.kotranslate
  } else if (lang === "jp" && music.jptranslate) {
    translatedText = music.jptranslate
  }

  if (translatedText) {
    const splitTranslate = translatedText.split("\n")
    setDefaultTranslate(splitTranslate)
    //함수를 이용해 객체 형태로 만든다.
    const defaultObj = makeDefaultObj(splitTranslate);
    //defaultValues를 위해 state를 지정한다.
    setDefaultFormValue(defaultObj);
    //react-hook-form의 초기상태를 설정한다.
    reset(defaultObj); 👈
  }
}, [music, lang])

//react-hook-form
const {
  register,
  handleSubmit,
  formState: { errors },
  reset, 👈
} = useForm({
    defaultValues: defaultFormValue,
  });
```

## ✅reset

reset 메서드는 전체 폼 상태와 필드의 참조, 구독을 초기화 하는 메서드다! 추가적으로, 공식문서에서는 다음과 같이 사용시 유의 사항을 전달한다.

이번 해결법의 경우 1번 구성 요소 값을 초기화 할때 defaultValues를 전달하며 초기화 한 것으로 이해 된다.🤔

> - 제어 컴포넌트 경우, Controller 구성 요소 값을 초기화 하려면 useForm에 defaultValues를 전달 해야 합니다.
> - reset API에 defaultValues가 제공되지 않으면 HTML의 기본 재설정 API가 호출되어 폼이 복원됩니다.
> - useForm의 useEffect가 호출되기 전에 reset을 호출하는 것을 피해야 합니다. useForm의 구독이 재설정된 상태를 업데이트하기 전에 reset이 신호를 보낼 수 있어야 하기 때문입니다.

```typescript
useEffect(() => {
  reset({
    data: "test",
  })
}, [isSubmitSuccessful])
```

## 📩마무리

어떻게 해결해야 할지 난감했는데 역시 공식문서에 다 답이 나와있었다.😅 이렇게 react-hook-form에 대해 하나 또 배워서 뿌듯하다.😎

## 🗂️참고 사이트

- <https://react-hook-form.com/docs/useform#defaultValues>
- <https://react-hook-form.com/docs/useform/reset>
