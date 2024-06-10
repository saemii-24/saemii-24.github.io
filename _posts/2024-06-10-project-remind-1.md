---
title: "프로젝트 리마인드 - 카드 필터링"
author: saemii
categories:
  - Project
tags:
  - [JavaScript, Project, React]
date: 2024-06-10
last_modified_at: 2024-06-10
---

## 📌들어가며

Card Check라는 프로젝트를 진행하면서 가장 중요했고, 구현 시간이 다소 소요되었던 부분은 카드를 필터링 하는 부분이었다. 이 부분에 대해 질문이 들어온 적이 있는데 설명을 버벅여서 부끄러웠던 기억이 있어서..😅 리마인드 겸 주요 개념을 복습해보고자 한다.

## ✅전체 코드

아래는 이 로직의 전체 코드다! 이제 차근차근 살펴보자.

```javascript
//회사 filter 함수 (하단 혜택 filter함수 콜백함수로 가지고 있음)
const handleFilterBtn = () => {
  setAllCardView(false)
  let thisData = [...cardData]

  let companyMapArr = company
    .filter((el) => el.active === true)
    .map((el) => el.company)
  let benefitMapArr = benefit
    .filter((el) => el.active === true)
    .map((el) => el.benefit)

  const filterObj = thisData.filter((card) => {
    const companyCondition = companyMapArr.includes(card.bank) //card.bank(은행이름)가 companyMapArr(선택 은행이름 배열)에 포함되어 있는가?
    const benefitCondition = benefitMapArr.every(
      (
        condition //모든 benefit condition을 만족하는가?
      ) => card.benefit.some((benefit) => Object.keys(benefit)[0] === condition) //card의 여러 benefit들 중 제시 된 condition을 만족하는 것이 있는가?
    )
    return companyCondition && benefitCondition
  })
  setFilterData(filterObj)
}
```

## ✅구현 상세

먼저 cardData를 복사해준다.

```javascript
const handleFilterBtn = () => {
  setAllCardView(false)
  let thisData = [...cardData]
}
```

cardData는 아래와 같은 객체를 담은 배열이다.

```javascript
  {
    id: 'kb-01',
    name: '노리2 체크카드(KB Pay)',
    bank: 'KB국민카드',
    cost: '연회비 없음',
    brand: ['국내전용', 'MASTER', 'VISA'],
    point: [
      { 생활: '커피, 모바일, 문화 10% 할인' },
      { 쇼핑: '뷰티, 편의점 5% 할인' },
      { 여행: '해외이용 2%, 공항라운지' },
    ],
    benefit: [
      { 커피: '스타벅스, 커피빈 10% 할인(월 할인한도 : 3천원)' },
      { 모바일: '구글플레이스토어, 앱스토어 10% 할인 (월 할인한도 : 5천원)' },
      { 문화: '인터파크 티켓 10% 할인 (월 할인한도 : 7천원)' },
      { 뷰티: '올리브영, 미용실 업종 5% 할인 (월 할인한도 : 2천원)' },
      { 편의점: 'GS25, CU 5% 할인 5% 할인 (월 할인한도 : 2천원)' },
      { 구독: '넷플릭스, 유튜브프리미엄 1만원 이상 시 1,000원 할인 (월 할인한도 : 2천원)' },
      { 배달: '배달의민족, 요기요 1만원 이상 시 1,000원 할인 (월 할인한도 : 1천원)' },
      { 이동통신: 'SKT, KT, LG U , Liiv M 5만원 이상 시 2,500원 할인 (월 할인한도 : 2천5백원)' },
      { 영화: 'CGV 1만원 이상 시 4,000원 할인 (월 할인한도 : 8천원)' },
      { 놀이공원: '에버랜드, 롯데월드 3만원 이상 시 15,000원 할인 (월 할인한도 : 1만5천원)' },
      { 기타: '오프라인 가맹점 KB Pay 결제 시 2% 추가 할인 (월 할인한도 : 3천원)' },
      { 기타: '온라인 가맹점 KB Pay 결제 시 2% 추가 할인 (월 할인한도: 2천원)' },
    ],
    image: '/image/card/kb_1.png',
  },
```

현재 서술하고 있는 코드에서는 생략되었지만, 위의 객체에서 active를 추가해 company 변수를 만들었는데, 이 active가 true인 경우 (즉 사용자가, 해당 카드사를 선택한 경우) 여기서 company 이름만을 선택한다.

즉 사용자가, KB국민은행, 우리은행 버튼을 클릭한경우 ['KB국민은행', '우리은행']과 같은 배열이 만들어져, companyMapArr에 할당된다.

비슷하게, 사용자가 선택한 혜택 버튼에 따라 ['커피', '모바일']등의 혜택 배열이 만들어지고, benefitMapArr에 할당된다.

```javascript
let companyMapArr = company
  .filter((el) => el.active === true)
  .map((el) => el.company)
let benefitMapArr = benefit
  .filter((el) => el.active === true)
  .map((el) => el.benefit)
```

이제 위에서 만든 사용자가 선택한 배열을 사용해 카드를 필터링 한다.

먼저 companyMapArr로 만든 사용자가 선택 한 카드사 이름이 담긴 배열에 해당

```javascript
const filterObj = thisData.filter((card) => {
  const companyCondition = companyMapArr.includes(card.bank)
  const benefitCondition = benefitMapArr.every((condition) =>
    card.benefit.some((benefit) => Object.keys(benefit)[0] === condition)
  )
  return companyCondition && benefitCondition
})
```
