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

(작성중~)
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

## ✅ring

## ✅peer

## ✅group
