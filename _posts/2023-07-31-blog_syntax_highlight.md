---
layout: post
title:  "Jekyll 블로그 코드블럭 문법 하이라이트 기능 추가"
date:   2023-07-31 23:30:00 +0900
category: dev
---

현재 jekyll blog 테마로 사용중인것은 [no-style-please](https://github.com/riggraz/no-style-please) 테마이다.

여기서 code block highlight 기능 관련해서는 직접 추가해주어야 하는것 같다.  

기존의 코드블록은 아래 이미지와 같이 따로 highlight 가 되지 않고 그냥 흰색 바탕에 글자 뿐이다.  

![/assets/images](/assets/images/2023/07/31/1.png)  

여기에 코드가 하이라이트 되도록 수정하자.  

<br /><br />

# Rouge

[rouge](https://github.com/rouge-ruby/rouge)는 루비 문법 하이라이터라고 한다.(자세한 설명은 rouge 링크를 참조)  

rouge 를 이용하여 코드블락 문법을 하이라이트 해주는 css 파일을 생성해서,  

생성된 css 파일을 현재 블로그에 적용하면 된다.  

먼저 rouge 를 설치하도록 하자.

~~~shell
gem install rouge
~~~

설치가 완료 되었으면 이제 rouge에서 제공하는 syntax higlight teme 중 하나를 골라서 css 파일을 생성하도록 하자.

[route-theme-preview](https://spsarolkar.github.io/rouge-theme-preview/) 웹사이트를 접속해서 원하는 테마를 먼저 고르자.  

나는 github 테마를 선택했다.

![/assets/images](/assets/images/2023/07/31/2.png)  


이제 선택한 테마를 가지고 rougify 명령어를 사용하여 syntax를 하이라이트 해주는 css 파일을 생성하도록 하자.

```shell
rougify style github > assets/css/syntax.css
```  

assets 폴더 안의 css 폴더 안에 *main.scss* 파일이 있어 이경로에 저장하도록 한다.

그리고 main.scss 파일을 열고 아래와 같이 생성된 *syntax.css* 파일을 추가하도록 하자.

~~~scss
@import "no-style-please";

@import "syntax.css";
~~~

이렇게 추가를 하고 또한군데 수정한 곳은  *no-style-please.scss* 이다.

*_sass* 폴더 안에 있고 아래와 같이 수정하여 코드 부분의 스타일을 코멘트 처리 하였음.

~~~scss
// code {
//   color: white;
//   background: black;
// }
~~~

그리고 마지막으로 해야할 작업은 *_config.yml* 파일을 수정하여 syntax_highlighter를 추가해주면 끝이다.

~~~yml
# _config.yml 파일 맨 아래에 다음과 같이 추가한다.

markdown: kramdown
kramdown:
  input: GFM
  syntax_highlighter: rouge
~~~

이렇게 하면 아래 그림처럼 코드 영역이 하이라이트 되어서 이쁘게 볼 수 가 있다 ^^.

![/assets/images](/assets/images/2023/07/31/3.png)

아마 이글을 읽고 있을때면 하이라이트 기능이 추가 되었기 때문에 코드부분에서  

괴리감을 느낄수 없을 것이다.

## 이렇게 해서 jekyll 블로그 코드하이라이트 기능 추가 끝!