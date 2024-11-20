---

title: "TailwindCSS (6) - Tailwind 기능 더 알아보기"
author: saemii
categories: - TailwindCSS
tags: - [Next.js, TailwindCSS]
date: 2024-11-12
last_modified_at: 2024-11-12
pin: true

---

## 📌시작하며

tailwind를 사용한지 꽤 오랜시간이 지났다. 쓰면서 느끼는 점은 확실히 className이 길어지면서 보기 불편해지는 건 맞지만, 사용하기 편리한 것 또한 부정할 수 없다는 것!

이번 글에서는 tailwind를 쓰면서 이상하리만큼 까먹던..(ㅎㅎ) 몇가지 속성과, _정말 편리했다..!_ 하는 내용을 정리해보고자 한다.

## ✅leading

[leading](https://tailwindcss.com/docs/line-height)은 line-height를 나타내는 className이다. 사실 tailwind가 웬만하면 거의 원래 css 이름과 비슷해서 사용하기 편리한것도 있는데, line-height의 경우 leading이라고 사용해야 해서 정말 안외워지던..이름이었다. 🥹 (그래서 책상 앞에 포스트잇 붙여놓은건 안 비밀..^^;)

어쨌든, leading은 크게 두 가지 방식으로 나눌 수 있는데, **'상대적'** 인 방식으로 주는 것과 **'절대적'** 인 방식으로 값을 주는 거다.

### ➡️ 상대적인 방식의 leading

| **클래스**        | **값**           | **설명**                          |
| ----------------- | ---------------- | --------------------------------- |
| `leading-none`    | `1` (100%)       | 현재 글자 크기의 100% (1배)       |
| `leading-tight`   | `1.25` (125%)    | 현재 글자 크기의 125% (1.25배)    |
| `leading-snug`    | `1.375` (137.5%) | 현재 글자 크기의 137.5% (1.375배) |
| `leading-normal`  | `1.5` (150%)     | 현재 글자 크기의 150% (1.5배)     |
| `leading-relaxed` | `1.625` (162.5%) | 현재 글자 크기의 162.5% (1.625배) |
| `leading-loose`   | `2` (200%)       | 현재 글자 크기의 200% (2배)       |

### ➡️ 절대적인 방식의 leading

| **클래스**   | **값**           | **설명**          |
| ------------ | ---------------- | ----------------- |
| `leading-3`  | `0.75rem` (12px) | 고정 줄 간격 12px |
| `leading-4`  | `1rem` (16px)    | 고정 줄 간격 16px |
| `leading-5`  | `1.25rem` (20px) | 고정 줄 간격 20px |
| `leading-6`  | `1.5rem` (24px)  | 고정 줄 간격 24px |
| `leading-7`  | `1.75rem` (28px) | 고정 줄 간격 28px |
| `leading-8`  | `2rem` (32px)    | 고정 줄 간격 32px |
| `leading-9`  | `2.25rem` (36px) | 고정 줄 간격 36px |
| `leading-10` | `2.5rem` (40px)  | 고정 줄 간격 40px |

줄간격은 가독성에 큰 영향을 미치는 부분이기도 한 만큼, 필요에 따라 적절히 사용해야 겠다. 😊

## ✅apply

작업을 하다 보면 flex를 활용한 가운데 정렬을 자주 사용하게 되고, 반복적으로 아래와 같은 코드를 작성하는 일이 많다.

```javascript
<div className="flex items-center justify-center">가운데 정렬</div>
```

@apply를 활용하면 이런 반복을 줄이고,여러 Tailwind CSS 클래스를 내가 지정하는 이름의 하나의 CSS 클래스에 결합할 수 있다.

```css
.center-flex {
  @apply flex items-center justify-center;
}
```

```javascript
<div className="flex items-center justify-center"></div>
```

이제 가운데 정렬은 아래와 같이 간결하게 사용할 수 있다.

```javascript
<div className="center-flex">가운데 정렬</div>
```

이 기능을 활용하면 특정 상황에 여러 css가 변경되어야 하는 경우에도 편리하게 사용할 수 있다.

```css
.btn-default {
  @apply px-4 py-2 bg-blue-200 rounded;
}
.btn-disable {
  @apply px-4 py-2 bg-gray-200 rounded;
}
```

```javascript
<button
  className={cn("mt-3", {
    "btn-default": status === "default",
    "btn-disable": status === "disabled",
  })}
  disabled={status === "disabled"}
>
  버튼
</button>
```

## ✅divide

여러 요소들이 쌓여있을 때 구분해줄 수 있는 경계선을 만든다. 이 클래스는 부모에게 주면 된다.

```javascript
<div class="divide-x-[5px]">
  <div>01</div>
  <div>02</div>
  <div>03</div>
</div>
```

### ➡️divide width

경계선의 width(border-width)를 조정한다.

- 가로 경계선: divide-x-\_
  - divide-x-2 → 가로 요소 간 좌우 경계선 너비를 2px로 설정한다.
- 세로 경계선: divide-y-\_
  - divide-y-4 → 세로 요소 간 상하 경계선 너비를 4px로 설정한다.
  - Reverse: 요소 순서가 뒤집힌 경우(flex-row-reverse 등), divide-x-reverse, divide-y-reverse를 사용하여 경계선 위치를 수정한다.

여기서 주의할 점은 다른 클래스에서(`mt-4`) 사용하는 숫자는 1이 4px를 나타내는 것이지만, divide-x-4는 border의 넓이가 숫자 그대로 4px이 된다는 것이다

### ➡️divide color

경계선의 색상을 결정한다.

- 색상 적용방법: divide-gray-500, divide-red-600 등

### ➡️divide Style

경계선의 스타일을 결정한다.
| Class | Properties |
|-------------------|-------------------------------------|
| `divide-solid` | `border-style: solid;` |
| `divide-dashed` | `border-style: dashed;` |
| `divide-dotted` | `border-style: dotted;` |
| `divide-double` | `border-style: double;` |
| `divide-none` | `border-style: none;` |

## ✅space

divide가 각 요소들을 구별하기 위해 선을 넣는 것이라면, space는 요소들 사이에 간격을 주는 방식이다.

- 수평: space-x-\_
- 수직: space-y-\_

이때 해당 class는 여러 요소를 묶는 부모에 주면 된다

```javascript
<div className="flex space-x-4">
  <div>01</div>
  <div>02</div>
  <div>03</div>
</div>
```

## ✅before & after

before와 after는 CSS의 가상 요소(pseudo-elements) 로, 실제 DOM에는 존재하지 않지만 CSS를 통해 스타일을 지정할 수 있는 요소다. Tailwind CSS에서도 이를 활용할 수 있다.

Tailwind는 기본적으로 before와 after를 지원하지 않으므로, 사용하려면 아래처럼 content 속성을 추가해 활성화해야 한다.

javascript
코드 복사

<div class="relative before:content-['🔥'] before:absolute before:top-0 before:left-0">
  이 문장을 주목하세요
</div>
🔍 Tailwind에서 before/after 설정하는 방법
Tailwind에서 before와 after를 사용하려면 클래스 이름 앞에 before: 또는 after:를 붙이면 된다. 이를 통해 Tailwind의 유틸리티 클래스들을 가상 요소에 직접 적용할 수 있다.

1️⃣ content 설정하기
CSS의 content는 가상 요소를 활성화하기 위해 반드시 설정해야 하는 속성이다. Tailwind에서는 이를 before:content-['텍스트'] 또는 before:content-none과 같은 방식으로 지정할 수 있다.

javascript
코드 복사

<div class="before:content-['✅'] before:text-green-500">
  성공 메시지
</div>
2️⃣ 스타일 지정하기
다른 Tailwind 클래스처럼 가상 요소에도 색상, 크기, 위치 등을 자유롭게 설정할 수 있다.

javascript
코드 복사

<div class="relative before:content-['✨'] before:text-yellow-500 before:mr-2 before:text-xl">
  반짝이는 텍스트
</div>
3️⃣ 호버 상태와 결합하기
가상 요소는 상태 클래스와도 결합 가능하다. 예를 들어, hover, focus 같은 상태에 따라 가상 요소의 스타일을 변경할 수 있다.

javascript
코드 복사

<div class="relative before:content-['🚀'] before:opacity-0 hover:before:opacity-100 hover:before:translate-x-2 transition-all">
  가상 요소를 확인하세요!
</div>
✨ Tailwind에서 before와 after를 사용할 때의 팁
가상 요소를 활성화하려면 반드시 content 속성을 설정해야 한다.
relative, absolute 같은 속성을 활용해 위치를 세밀히 조정할 수 있다.
상태 클래스(hover, focus 등)와 조합하면 더 다양한 UI 효과를 만들 수 있다.
이렇게 before와 after를 활용하면 CSS의 강력한 기능과 Tailwind의 편리함을 결합해 창의적인 UI를 구현할 수 있다. 🎨

## ✅truncate

작업중 넘치는 텍스트를 ... 표시하기 위해 기존에 사용했던 방식대로 white-space text overflow 등을 사용하려고, tailwind 클래스를 찾아보려고 했는데, tailwind에서는 ` `라는 class 이름으로 훨씬 간단하게 관련된 css를 제공하고 있었다.

| 클래스          | 속성                                                                        | 의미                                                                        |
| --------------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `truncate`      | `overflow: hidden;`<br>`text-overflow: ellipsis;`<br>`white-space: nowrap;` | 텍스트가 요소의 크기를 초과할 때, 텍스트 끝에 '...'을 표시하여 잘리게 한다. |
| `text-ellipsis` | `text-overflow: ellipsis;`                                                  | 텍스트가 넘칠 때 '...'으로 표시하며, `truncate`와 비슷한 기능을 한다.       |
| `text-clip`     | `text-overflow: clip;`                                                      | 텍스트가 넘칠 때 '...'을 표시하지 않고, 텍스트가 잘리도록 한다.             |

### ➡️truncate vs text-ellipsis

truncate는 '...'으로 나타내기 위한 test-ellipsis의 차이점은, 실제 렌더링 되는 css를 보면 text-ellipsis는 사실 `text-overflow: ellipsis`만 적용하는 클래스라는 것이다. `text-ellipsis`는 텍스트가 넘칠 때 '...'을 표시하는 역할만 하며, 다른 스타일은 포함되지 않기 때문에, 텍스트가 한 줄로 유지되거나 잘리는 방식은 별도로 지정되지 않아 별도로 추가 설정이 필요하거나, 필요할 때 사용할 수 있다.

## ✅aspect-ratio

aspect-ratio는 [CSS 속성](https://developer.mozilla.org/en-US/docs/Web/CSS/aspect-ratio)에 정의되어있는 속성과 동일한 속성으로, 원하는 **종횡비**를 유지할 수 있게 해주는 기능이다!

지정되어 있는 값은 다음과 같다.

| 클래스          | 설명               | 예시 비율 |
| --------------- | ------------------ | --------- |
| `aspect-auto`   | 기본 비율 유지     | 원본 비율 |
| `aspect-square` | 정사각형 비율      | 1:1       |
| `aspect-video`  | 비디오 비율 (16:9) | 16:9      |

원하는 값은 다른 tailwind 작성법과 동일하게 `aspect-[4/3]`과 같은 방식으로 적어준다.
이때 4는 가로, 3은 세로값이 된다.

## ✅outline & ring

tailwind에서는 border 바깥쪽에 선을 그릴 수 있는 방식으로 outline과 ling을 제시한다.
outline이란 건 기본적으로 CSS에 있는 속성으로, 요소의 외곽선을 의미하고, 포커스 가능 요소에서 포커스를 나타내는 데 자주 사용된다.

그렇다면 ring은 무엇일까? 먼저 ring은 box-shadow로 생성하는 요소의 외곽선이다! outline 처럼 요소가 포커스 상태일 때 강조를 위해 사용되며, box-shadow로 구현되기 때문에 border와 outline과는 독립적으로 작동한다.

### ➡️outline vs ring

ring과 outline의 가장 큰 차이점은 위에서 살펴본 것 처럼, 어떤 CSS 속성을 사용했냐는 것으로, ring은 box-shadow를 사용하는 만큼, 색상, opacity, blur, offset 등 좀더 유연하고 다양한 커스텀이 가능하다. 하지만 outline은 두께, 색상 조정등 ring과 비교해 단순한 CSS 조작만 가능하다.

| class                  | 설명                                  |
| ---------------------- | ------------------------------------- |
| outline                | 외곽선 추가                           |
| outline-{size}         | 두께 조절                             |
| outline-{color}        | 색상 지정                             |
| outline-offset-{value} | 요소와 외곽선(outline) 사이 간격 지정 |

| class                | 설명                               |
| -------------------- | ---------------------------------- |
| ring                 | 외곽선 추가                        |
| ring-{size}          | 두께 조절                          |
| ring-{color}         | 색상 지정                          |
| ring-opacity-{value} | 투명도 지정                        |
| ring-offset-{value}  | 요소와 외곽선(ring) 사이 간격 지정 |
| ring-offset-{color}  | 요소와 외곽선(ring) 사이 색상 지정 |

## ✅peer

`peer`는 peer는 형제 요소간의 상태를 기반으로 스타일을 적용할 수 있게 해준다! 이는 css로 작성하지 않는 방식에서 오는 불편함을 해소하기 위한 방식이기도 하다.

예를 들어, checkbox가 체크 되었을 때 '동의' 라는 label은 파란색으로 변해야 된다고 생각해보자.

state를 이용해 조건으로 관리할 수 있겠지만 peer 클래스를 사용한다면 CSS 만으로도 해결할 수 있다.

```javascript
<div>
  <input id="name" type="text" class="peer hidden" />
  <label for="name" class="peer-checked:text-blue-500">
    동의
  </label>
</div>
```

1. 상태를 변경하는 기준 요소에 peer 클래스 추가

   - 상태값이 변하는 조건의 기준이 되는 형제 요소에 peer 클래스를 추가한다.

   - 위 예제에서는 체크박스의 선택 상태(true 또는 false)가 기준이 되므로 `<input>` 태그에 peer 클래스를 부여했다.

2. 변경된 상태에 따라 스타일 적용

   - 상태값의 변경에 따라 CSS를 변경할 대상 요소에 조건 클래스를 추가한다.
   - 위 예제에서는 `<label>`에 peer-checked를 사용해, 기준이 되는 peer 요소가 체크되었을 때 텍스트 색상이 파란색(text-blue-500)으로 변경되도록 했다.

이런식으로 특정 상태에 따라 형제요소가 영향을 받게 할 수 있으며, 자주쓰는 형태는 다음과 같다.

| class         | 상태                                     |
| ------------- | ---------------------------------------- |
| peer-hover    | peer 요소에 마우스를 올릴 때.            |
| peer-focus    | peer 요소가 포커스될 때.                 |
| peer-checked  | peer-checked: peer 요소가 체크되었을 때. |
| peer-disabled | peer 요소가 비활성화되었을 때            |

## ✅group

group은 위에서 살펴본 peer와 사용방식은 거의 비슷하지만, 형제 요소가 아니라 부모-자식 요소끼리의 그룹이라는 것에 차이가 있다.
사용방식은 아래와 같다.

1. 부모 요소에 group 클래스를 추가.
2. 자식 요소에서 `group-hover`, `group-focus` 등 부모의 특정 상태값을 이용해 스타일 변경

## ✅sr-only

`sr-only` 에서 `sr`은 **screen-readers** 를 말하는 것으로, 웹 화면상에는 보이지 않지만 스크린 리더기가 인식할 수 있어야 한다는 의미를 뜻한다.

예를 들어 아이콘만으로 메뉴를 만들었을 때, 스크린 리더는 해당 아이콘이 무엇을 의미하는지 알 수 없으므로, 이를 방지하기 위해 '메뉴'라는 텍스트를 제공하되, 실제 웹 사이트에서는 보이지 않도록 하는 것이다!

이를 통해 웹 접근성을 높여 더 나은 사용자 경험을 제공할 수 있다.

만약 다시 보여져야 한다면 `not-sr-only`를 사용하면 된다.
