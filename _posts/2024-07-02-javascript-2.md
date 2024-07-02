---
title: "Drag and Drop (DnD) 구현"
author: saemii
categories:
  - JavaScript
tags:
  - [JavaScript, React]
date: 2024-07-02
last_modified_at: 2024-07-02
pin: false
---

## 📌시작하며

간단한 토이 프로젝트를 진행하고 있다! 사실 코드만 간단히 연습용으로 써보려고 했는데 역시 뭐든 각이 잡혀야(?) 더 재밌는 법이라...!😌

프로젝트는 포켓몬API와 GraphQL을 활용하는 건데 이 데이터를 어떻게 재밌게 활용해볼까 하다가, 어렸을 때 닌텐도로 포켓몬 게임을 했던 것을 떠올려봤다.

플레이어가 가지고 있는 스타팅 포켓몬은 각각 순서가 있어서, 가장 먼저 자리잡은 포켓몬은 플레이어를 따라다니거나, 맨 처음에 배틀 순서가 되곤 해서 배틀 시작 전에 이런 순서를 나름대로 조정하곤 했는데, 이걸 Drag and Drop으로 조정했던 것 같다. (어렸을 때 기억이라 정확하진 않다...😅)

어쨌든, 이번에 이걸 직접 구현해보려고 DnD 구현 방법을 찾다가, 아예 Web API에서 제공하고 있다는 것을 알게 되었고! 라이브러리를 사용하지않고, 나름대로 구현한 내용을 정리하고자 한다.

## ✅drag 이벤트

### ➡️draggble

`drag`이벤트는 사용자가 요소나 텍스트를 드래그하는 동안 매 수백 밀리초마다 발생하는 이벤트다. 이런 이벤트는 기본적인 모든 이벤트에서 작동하는 것이 아니라 해당 요소가 `drag`가 가능하다는 것을 먼저 알려줘야 한다.
키워드는 `draggable`로, `drag`를 원하는 요소에 작성해주면 된다.

```javascript
<div draggable="true">드래그 가능</div>
<div>드래그 불가능</div>
```

### ➡️drag 이벤트 종류

DnD를 생각해보면 drag 이벤트에는 다양한 종류가 있을 수 밖에 없다. 예를 들어, drag를 시작했을 때, drop했을 때 등!
즉 관련된 이벤트도 종류가 여럿 있다.

| 이벤트    | 이벤트 핸들러 | 설명                                                                                          |
| --------- | ------------- | --------------------------------------------------------------------------------------------- |
| drag      | ondrag        | 요소나 텍스트 블록을 드래그 할 때 발생한다.                                                   |
| dragend   | ondragend     | 드래그를 끝냈을 때 발생한다. (마우스 버튼을 떼거나 ESC 키를 누를 때)                          |
| dragenter | ondragenter   | 드래그한 요소나 텍스트 블록을 적합한 드롭 대상위에 올라갔을 때 발생한다.                      |
| dragexit  | ondragexit    | 요소가 더 이상 드래그의 직접적인 대상이 아닐 때 발생한다.                                     |
| dragleave | ondragleave   | 드래그하는 요소나 텍스트 블록이 적합한 드롭 대상에서 벗어났을 때 발생한다.                    |
| dragover  | ondragover    | 요소나 텍스트 블록을 적합한 드롭 대상 위로 지나갈 때 발생한다. (매 수백 밀리초마다 발생한다.) |
| dragstart | ondragstart   | 사용자가 요소나 텍스트 블록을 드래그하기 시작했을 때 발생한다.                                |
| drop      | ondrop        | 요소나 텍스트 블록을 적합한 드롭 대상에 드롭했을 때 발생한다.                                 |

이 중에서, DnD 구현에 사용한 이벤트는 `ondragenter`, `ondragstart`, `ondragend` 3가지다.

## ✅구현 흐름

나는 현재 Next.js를 이용하고 있어서 생각했던 흐름은 다음과 같다.

1. api를 받아온다.
2. 사용자가 선택한 스타팅 포켓몬을 배열 형태로 전역 상태관리한다.
3. 사용자가 드래그 한 내용을 바탕으로 상태를 업데이트 한다.
4. 해당 내용을 렌더링 한다.

1번과 2번 내용을 생략하고 간단한 예제 데이터를 가지고 작성한 코드는 다음과 같다.

### ➡️state 정의

필요한 state를 정의한다. 예제 데이터 arr과 drag하는 item과 drop자리에 있는 item의 state를 정의해주었다.

```typescript
//데이터 배열
let [arr, setArr] = useState<string[]>([
  "피카츄1",
  "라이츄2",
  "파이리3",
  "꼬북이4",
])

//drag하는 item
let [dragItem, setDragItem] = useState<HTMLDivElement | null>(null)

//drop하는 위치에 있는 item
let [destinationItem, setDestinationItem] = useState<HTMLDivElement | null>(
  null
)
```

### ➡️이벤트 정의

이벤트 종류는 3가지를 사용한다.

1. 현재 drag 하고 있는 아이템을 찾기
2. drop되는 위치 아이템 찾기
3. drop 이후 arr 업데이트 하기

```typescript
//현재 드래그 하는 아이템은 무엇인가?
const dragStart = (e: React.DragEvent<HTMLDivElement>) => {
  const targetItem = e.target as HTMLDivElement
  setDragItem(targetItem)
}

//어떤 아이템위로 드래그 되는가?
const dragDestination = (e: React.DragEvent<HTMLDivElement>) => {
  const targetItem = e.target as HTMLDivElement
  setDestinationItem(targetItem)
}

//drop후 데이터를 재정비한다.
const dropNow = () => {
  // drop 자리의 인덱스 찾기
  const destinationIndex = arr.findIndex(
    (item) => destinationItem?.innerText === item
  )

  // drag 자리의 인덱스 찾기
  const dragIndex = arr.findIndex((item) => dragItem?.innerText === item)

  // arr 순서 바꾸기
  if (
    dragIndex !== -1 &&
    destinationIndex !== -1 &&
    //drag하는 인덱스와 destination 인덱스가 같으면 굳이 실행할 필요가 없다.
    dragIndex !== destinationIndex
  ) {
    let newArr = [...arr]
    //drag 하는 item을 꺼내고, newArr에서 해당 item을 삭제함
    const [draggedItem] = newArr.splice(dragIndex, 1)

    //꺼낸 아이템을 적절한 위치에 넣음
    newArr.splice(destinationIndex, 0, draggedItem)
    setArr(newArr)
  }
}
```

### ➡️렌더링

위에서 작성한 것과 같이 `draggable` 표시와 정의해 둔 함수를 넣어주면 된다.

```typescript
//생략
{
  arr.map((item, i) => {
    return (
      <div
        key={i}
        className={`bg-red-100 box-${i + 1} h-[300px]`}
        draggable
        onDragEnter={(e) => {
          dragDestination(e)
        }}
        onDragStart={(e) => {
          dragStart(e)
        }}
        onDragEnd={() => {
          dropNow()
        }}
      >
        {item}
      </div>
    )
  })
}
```

## 🗂️참고 사이트

- <https://developer.mozilla.org/ko/docs/Web/API/HTML_Drag_and_Drop_API>
- <https://developer.mozilla.org/ko/docs/Web/API/HTMLElement/drag_event>
- <https://qiita.com/sueasen/items/2a39e709ffac6ff041f5>
