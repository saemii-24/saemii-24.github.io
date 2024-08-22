---
title: "파이썬 기본 문법 - 입력과 출력"
author: saemii
categories:
  - JavaScript
tags:
  - [Python]
date: 2024-08-19
last_modified_at: 2024-08-19
pin: true
---

## 📌시작하며

파이썬 강의 수강 중... 앞에 부분은 어렵지 않아서 가벼운 맘으로 보고 있었는데, 입출력 부분 부터 난이도가 올라가면서 새로 정리가 필요해졋다. 다행인건 흥미도도 쭉쭉 올라가는 중이란 것.😎 그래서 오늘은 파이썬 파일 I/O 방식에 대해 정리해보고자 한다.

## ✅기본적인 파일 열기

파이선에서 파일을 여는 핵심은 `open()` 함수다!
이후 파일은 `close()`를 이용해 닫을 수 있다.

```python
file = open('file_path', 'mode')
```

`'file_path'`: 열고 싶은 파일의 경로로, 상대 경로('./example.txt')나 절대 경로('/home/user/example.txt')를 사용할 수 있다.
`'mode'`: 파일을 열 때 사용할 모드를 지정한다.

### ➡️파일 열기 모드

| 모드  | 설명                                                                                                 |
| ----- | ---------------------------------------------------------------------------------------------------- |
| `'r'` | **Read** - 기본값. 파일을 읽기 전용으로 연다. 파일이 존재하지 않으면 오류가 발생한다.                |
| `'a'` | **Append** - 파일을 추가 모드로 연다. 파일이 없으면 새로 생성된다.                                   |
| `'w'` | **Write** - 파일을 쓰기 전용으로 연다. 파일이 존재하면 기존 내용을 덮어쓰며, 파일이 없으면 생성된다. |
| `'x'` | **Create** - 파일을 새로 생성하며, 파일이 이미 존재하는 경우 오류가 발생헌다.                        |
| `'t'` | **Text** - 기본값. 파일을 텍스트 모드로 처리한다.                                                    |
| `'b'` | **Binary** - 파일을 바이너리 모드로 처리한다. (예: 이미지 파일)                                      |

## ✅전체 파일 읽기

위의 예제에서는 단순히 파일을 열었을 뿐 그 안의 내용에 접근하고 있지 않았다. 파일을 읽기 위해서는 `read()` 메서드를 사용한다.

```python
# 파일을 읽기 모드로 연다.
with open('example.txt', 'r') as file:
    content = file.read()

# 파일 내용을 출력한다.
print(content)
```

여기서 `with`은 자원을 열 때 자원을 사용한 후 자동으로 닫아주기 위해 사용되었다.
`with` 블록이 끝나면 파일 핸들이 자동으로 닫히고 자원이 해제되므로, `file.close()`를 명시적으로 호출할 필요가 없다.

## ✅파일 한 줄씩 읽기

파일을 한 줄씩 읽을 땐 `readline()` 이나 `readlines()` 메서드를 사용한다.

- **readline()**: 파일에서 한 줄씩 읽음

```python
with open('example.txt', 'r') as file:
    line = file.readline()
    while line:
        print(line.strip())
        line = file.readline()
```

- **readlines()**: 파일의 모든 줄을 리스트로 반환함

```python
with open('example.txt', 'r') as file:
    lines = file.readlines()

for line in lines:
    print(line.strip())
```

## ✅문자열 파일에 기록하기

`write()` 함수를 이용해 문자열을 파일에 기록한다. 이때 문자열 끝해 개행 문자가 추가되진 않는다.

```python
with open('example.txt', 'w') as file:
    file.write("Hello, world!\n")
```

먼저 example.txt를 w(쓰기모드)로 연다. 이때 해당 파일이 존재하지 않으면 새로 생성한다.
해당 파일에 `Hello, world!`를 쓰고 개행함을 나타낸다.

## ✅문자열 파일에 기록하기

`writelines()` 함수는를 이용해 여러 문자열을 파일에 기록한다. **'여러 문자열'** 은 리스트 형태로 들어오는 문자열들을 말한다.

```python
# 문자열의 리스트를 파일에 기록하기
lines = [
    "Hello, world!\n",
    "Hello, earth!\n",
    "Hello, everyone!\n",
]

with open('example.txt', 'a') as file:
    file.writelines(lines)
```

writelines()를 통해 문자열 리스트를 받고 각 문자열을 파일에 작성한다. 위의 예제에서는 a 모드로 해당 파일을 열었기 때문에 기존 파일 끝에 데이터를 추가하며, 리스트 각 문자열에 줄 바꿈을 추가한다.

## 🗂️참고 사이트

- <https://www.w3schools.com/python/python_file_handling.asp>
- <https://www.w3schools.com/python/ref_file_readline.asp>
- <https://www.w3schools.com/python/python_file_write.asp>
