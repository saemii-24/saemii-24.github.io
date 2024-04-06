---
title: "TailwindCSS 세팅 (3) - 자주 쓰는 CSS 정리"
author: saemii
categories:
  - TailwindCSS
tags:
  - [Next.js, TailwindCSS]
date: 2024-02-27
last_modified_at: 2024-02-27
pin: true
---

## 📌시작하며

프로젝트를 시작하고 Tailwind를 사용하다 보니 자꾸 똑같은 속성의 클래스명을 헷갈려서, 자주 쓰는 내용을 정리해보면 좋을 것 같아 포스팅을 작성한다.

> 이 글은 아래와 같이 이어집니다.
>
> - [TailwindCSS 세팅(1)](https://saemii-24.github.io/posts/tailwindcss-1/)
> - [TailwindCSS 세팅(2)](https://saemii-24.github.io/posts/tailwindcss-2/)
> - [TailwindCSS 세팅(3)](https://saemii-24.github.io/posts/tailwindcss-3/)
> - [TailwindCSS 세팅(4)](https://saemii-24.github.io/posts/tailwindcss-4/)

## 🅰️ font

<table>
  <tr>
    <th>속성</th>
    <th>작성법</th>
  </tr>
  <tr>
    <td>Font Size</td>
    <td>text-[xs~9xl]</td>
  </tr>
  <tr>
    <td>Font Weight</td>
    <td>text-[thin~black]</td>
  </tr>
  <tr>
    <td>Letter Spacing</td>
    <td>tracking-[tighter~widest]</td>
  </tr>
  <tr>
    <td>Line Height</td>
    <td>leading-[3~10]</td>
  </tr>
  <tr>
    <td>Text align</td>
    <td>text-[left~end]</td>
  </tr>
  <tr>
    <td>Text Overflow</td>
    <td>truncate, text-ellipsis, text-clip</td>
  </tr>
</table>

## ✒️border

<table>
  <tr>
    <th>속성</th>
    <th>작성법</th>
  </tr>
  <tr>
    <td>Border Radius</td>
    <td>rounded-[md~full]</td>
  </tr>
    <tr>
    <td>Border Width</td>
    <td>border-[0~8 (2씩 증가)]</td>
  </tr>
    <tr>
    <td>Border Color</td>
    <td>border-[color]</td>
  </tr>
</table>

## 📐Layout

<table>
  <tr>
    <th>속성</th>
    <th>작성법</th>
  </tr>
  <tr>
    <td>Aspect Ratio</td>
    <td>aspect-[auto, squre, video]</td>
  </tr>
  <tr>
    <td>Position</td>
    <td>static, fixed, absolute, relatvie, sticky</td>
  </tr>
  <tr>
    <td>Top / Right / Bottom / Left</td>
    <td>inset-[number] / 방향-[number]</td>
  </tr>
  <tr>
    <td>Visibility</td>
    <td>visible, invisible, collapse</td>
  </tr>
  <tr>
    <td>Z-Index</td>
    <td>z-[number]</td>
  </tr>
  <tr>
    <td>Overflow</td>
    <td>overflow-[auto, hidden, scroll]</td>
  </tr>
  <tr>
    <td>Display</td>
    <td>block, inline-block, flex, grid, table</td>
  </tr>
</table>

## 🎁Size

<table>
  <tr>
    <th>속성</th>
    <th>작성법</th>
  </tr>
  <tr>
    <td>Width / Height</td>
    <td>w-[number] h-[number]</td>
  </tr>
   <tr>
    <td>Min - Width / Height</td>
    <td>min-w-[number], min-h-[number]</td>
  </tr>
   <tr>
    <td>Max - Width / Height</td>
    <td>max-w-[number], max-h-[number]</td>
  </tr>
</table>

## ❤️Flex

<table>
  <tr>
    <th>속성</th>
    <th>작성법</th>
  </tr>
  <tr>
    <td>Flex Wrap</td>
    <td>flex-wrap, flex-wrap-reverse, flex-nowrap</td>
  </tr>
  <tr>
    <td>Flex Grow</td>
    <td>grow, grow-0</td>
  </tr>
  <tr>
    <td>Flex Shrink</td>
    <td>shrink, shrink-0</td>
  </tr>
  <tr>
    <td>Flex Basis</td>
    <td>basis-[number]</td>
  </tr>
  <tr>
    <td>Order</td>
    <td>order-[number]</td>
  </tr>
  <tr>
    <td>Justify Content</td>
    <td>justify-[normal, start, end, between, center]</td>
  </tr>
  <tr>
    <td>Justify Items</td>
    <td>justify-items-[start, end, center]</td>
  </tr>
  <tr>
    <td>Align Content</td>
    <td>Content-[normal, start, end, between, center]</td>
  </tr>
  <tr>
    <td>Align Items</td>
    <td>items-[start, end, center]</td>
  </tr>
</table>

## 🍀Grid

<table>
  <tr>
    <th>속성</th>
    <th>작성법</th>
  </tr>
  <tr>
    <td>Grid Template Columns</td>
    <td>grid-cols-[number]</td>
  </tr>
  <tr>
    <td>Grid Template Rows</td>
    <td>grid-rows-[number]</td>
  </tr>
  <tr>
    <td>Grid Column Start / End</td>
    <td>col-auto<br/> col-span-[number] <br/>
        col-start-[number]<br/>col-end-[number]
    </td>
  </tr>
</table>

## 💟Container

사이즈별 다른 속성 작성시 작은 것 부터 적용된다. 즉 small 사이즈 속성이 default가 되고 large 사이즈를 추가적으로 작성해야 하는 것이다. 예를들어 `bg-slate-100 lg:bg-slate-500` 로 작성하면 100이 default 값, lg일때 500값으로 덮어 씌운다.

<table>
  <tr>
    <th>Breakpoint</th>
    <th>Properties</th>
  </tr>
  <tr>
    <td>None</td>
    <td>width: 100%;</td>
  </tr>
  <tr>
    <td>sm (640px)</td>
    <td>max-width: 640px;</td>
  </tr>
  <tr>
    <td>md (768px)</td>
    <td>max-width: 768px;</td>
  </tr>
  <tr>
    <td>lg (1024px)</td>
    <td>max-width: 1024px;</td>
  </tr>
  <tr>
    <td>xl (1280px)</td>
    <td>max-width: 1280px;</td>
  </tr>
  <tr>
    <td>2xl (1536px)</td>
    <td>max-width: 1536px;</td>
  </tr>
</table>

## 📩마무리

tailwind의 class 자체가 직관적이기 때문에 지금은 헷갈려도, 계속 쓰다보면 바로바로 떠올릴 수 있을 것 같다! 🥰

## 🗂️참고 사이트

- <https://tailwindcss.com/docs/installation>
