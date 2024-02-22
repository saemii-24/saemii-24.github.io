---
title: "Chirpy 설치와 커스텀"
author: saemii
categories:
  - Blog
tags:
  - [Blog, Web]
date: 2024-02-22
last_modified_at: 2024-02-22
pin: true
---

## 📌시작하며

개발 공부를 시작하고 지금까지 notion으로 많은 양을 정리해왔다. 하지만 notion으로 정리하면 아무래도 블로그보단 폐쇄적인 공간인 만큼 작성한 글을 누군가에게 보여주기 애매한 부분이 있었다.

아마 많은 개발자가 그렇겠지만.. 그 동안 다른 개발자분들의 블로그를 통해 공부한 부분이 많기에, 블로그를 시작해보면 어떨까? 생각하게 되었고, 블로그 개설을 시작했다.

마음에 드는 테마를 찾은 것도 잠시.. 오류 하나 해결하면 오류가 날 반기며.. 만났던 오류들은 또 만나게 되니 한 번 정리해보고자 한다.

## ✅Deploy가 되지 않을 때

테마 버전을 업데이트 하고 깃허브에 업로드하니 build는 되는데 deploy가 안되는 일이 발생했다. 이 경우에는 `.github/workflows/pages-deploy.yml.hook` 이 파일의 `.hook` 부분을 지워 `.yml`로 만들고 push 해주면 간단히 해결 된다.

## ✅Author가 제대로 표시되지 않을 때

`authors.yml` 파일에서 수정하지 않았기 때문이다. 아래와 같이 수정해준 다음, 글 작성 시 상단에 `author: 자신의 이름`을 작성해주면 url와 author 이름도 제대로 표시된다.

```
자신의 이름:
  name: 자신의 이름
  url: https://github.com/자신의 이름/
```

## ✅Sidebar 아이콘 커스텀

`contact.yml` 파일을 수정한다. 트위터는 사용하지 않을 것이므로 아래와 같이 `#`을 이용해 주석처리 해주었다.

```
# - type: twitter
#   icon: "fa-brands fa-x-twitter"
```

## ✅Code block 개행 되지 않을 때 (newline doesn't work)

처음 글에서 Codeblock을 자주 사용했는데 개행이 되지 않아 해결을 위해 꽤 시간을 소요했는데, `_config.yml` 파일의 문제였다. 이 문제를 기존 버전으로 reset 하니 제대로 다음 줄로 작성된다.
사실 이 문제는 어떤 부분이 정확히 문제였는지는 모르곘다. 혹시 해서 `_config.yml`파일을 수정하니 개행이 되어서...
이 부분은 추가적으로 확인이 필요할 듯 하다.

## ✅Home Page에 아무것도 표시되지 않을 때

이 부분은 글 작성시 `pin: true` 를 하지 않았기 때문이었다. 이 부분이 `false`인 경우에는 home에는 보이지 않지만 다른 페이지에서는 확인이 가능하다.

```
---
title: "왜 표시가 안될까?!"
pin: true
---
```

## ✅JS 파일이 없다고 할 때

페이지를 만들고 몇 가지 기능이 제대로 작동하지 않는 경우, 혹은 github page를 개설했는데 제대로 표시되지 않는 경우 JS 파일이 없다는 오류를 볼 수 있다. 이 부분은 필요한 패키지를 먼저 install 하고 build 해주면 된다.
그 다음 gitignore에서 `assets/js/dist` 이 부분을 주석처리 해준다.

```bash
npm install && npm run build
```

## ✅exit code 16 Error

github action 중 exit code 16 Error 오류의 경우 다음과 같은 명령어를 실행해 해결할 수 있다.

```
bundle lock --add-platform x86_64-linux
```

## 📩기타 오류 발생 시

구글링도 도움이 되지만, [Chirpy 깃허브 이슈](https://github.com/cotes2020/jekyll-theme-chirpy/issues) 에서 Issues에 검색하는 것도 좋았다. 의외로 한국인 분들의 질문도 많다는 점! (사이버 세상에서 느끼는 익숙한 언어의 반가움🤭)
