---
layout: post
title:  "깃허브 페이지 시작"
date:   2023-07-19 19:52:44 +0900
---

[구글 블로그](https://www.blogger.com/)에서 [깃허브 페이지](https://pages.github.com/)로
블로그를 넘어왔다.  

가장 큰 이유로는 로컬에서 작업하고, github에 push만 하면 하나의 포스트가 등록이 된다.

깃허브 페이지는 [jekyll](https://jekyllrb.com/)을 이용해서 정적웹사이트를 호스팅해준다.

그저 나는 포스트만 작성하면 된다.

일반 포스팅하고 가장 큰 차이점이라면,  **[마크다운](https://www.markdownguide.org/getting-started/)** 문법으로 글을 작성하는 것이다.

jekyll에도 여러가지 wordpress와 같이 여러가지 테마가 있는대,

가장 간단한 테마를 선택했다.

선택한 테마는 [**no-style-please**](https://github.com/riggraz/no-style-please) 라는 정말 간단한 테마다.

기존 구글블로거에 있는 블로그들을 jekyll로 불러오는 작업과,

깃헙페이지를 셋업하고 등등.. 한 작업들이 많다...
  
<br/><br/><br/><br/>

# 구글 블로거 포스트 추출

가장 먼저 작업해야할것은 구글 블로거에 이미 작성한 글들을 추출하는 일이다.

![포스트 추출 버튼](/assets/images/2023/07/19/1.png)

우측의 *설정* 탭을 누르고 쭉 내리다 보면 *Manage Blog* 섹션이 나오는데  

*Back up content* 버튼을 누르면 현재까지 작성된 포스트들을 xml 파일로 추출해준다.

![추출된 포스트 내용](/assets/images/2023/07/19/2.jpeg)

<br/><br/><br/><br/>

# 추출된 포스트 jekyll로 불러오기

Google Bard를 이용해서 *"How to migrate google blogger post to jekyll post?"* 질문의 답변을  

이용하여 블로그 포스트를 불러오기 하였다.

1. Install the jekyll-import gem. You can do this by running the following command in your terminal:   
    ```gem install jekyll-import``` 

2. Move the XML file from Step 1 to the root directory of your new Jekyll site.  

3. Run the following command in your terminal to import your Blogger posts into Jekyll:  
    ```ruby -r rubygems -e 'require "jekyll-import"; JekyllImport::Importers::Blogger.run({ "source" => "path/to/export.xml" })'```

위와 같이 작업을 하면, *_posts* 폴더내에 블로거 포스트들이 생성된다.  

<br/><br/><br/><br/>

# 블로거에 저장된 이미지 파일들 옮기기

구글 블로거를 임포트를 하면 내용이 불려오긴 하지만,  

html태그등 완전하게 markdown 형식으로 변환 되어있지 않다.

또한 블로거에서 포스트를 작성할때 업로드한 이미지들도 블로거 서버에 남아 있기 때문에,

아직도 콘텐츠들은 블로거에 종속되어 있다.

이문제를 해결 하기 위해 2가지 방법을 취했다.

1. html태그를 전부 없애고 markdown 형식으로 변환하기
    
    구글 블로거에 보여지는 그대로를 완벽하게 따라 할수는 없지만, 최소한 ```<a>, <img>, <strong>, <i>, etc...``` 태그등은 markdown 형식으로 변환 할수가 있다.
  

2. 블로거에 이용된 이미지 리소스들 (.png, .gif) 파일들을 일일히 다운 받기  
  
    *crul*을 이용하여 이미지 리소스를 다운받고 이미지 링크들 변경하기  
  
<br />

[html to markdown converter](https://codebeautify.org/html-to-markdown)를 이용하여 html 태그들을 변환 하였다.  

이미지 파일들은 아래의 터미널에서 curl 명령어를 이용하여 다운받는다.  

```curl "{이미지파일주소}" -o xxx.png```  

![terminal curl example](/assets/images/2023/07/19/3.jpeg)
\[터미널에서 curl 명령어를 이용해 이미지를 다운 받는 사진\]

그렇게 노가다 작업을 통해서 임포트한 블로거 포스트들을 마크다운 형태로, 이미지파일들도 프로젝트 assets 폴더 내에 저장후 링크들도 assets폴더내의 이미지 파일을 가리키도록 노가다 작업을 진행했다.

<br /><br /><br /><br />

# 깃허브 페이지 셋업

[깃허브 페이지 설정 관련 문서](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll#adding-a-theme)를 참조해서 작업하였고,  

여기서 가장 중요한것은 **_config.yml** 파일 내부의 ```theme: blahblah```라고 되어있는 부분을  

```remote_theme: riggraz/no-style-please```으로 remote_theme으로 변경하는거 이게 가장 중요하다.  

깃허브 페이지 설정 관련 문서를 꼼꼼히 읽지않고 넘어가다가... 한참을 해맨후에 remote_theme 저거 하나때문에  

거진 2~3시간을 허비 하였다....  


![](/assets/images/2023/07/19/4.jpeg)
\[이미지에서 보여주듯이 *The no-style-please theme could not be found*\]

하지만 그러는 동안 gem, ruby 관련 version들을 맞추는것,  

그리고 결국 jekyll 관련 설정을 하기 위해서 깃허브 페이지의 jekyll, ruby를 어느 버전으로 사용하고  

그런것들을 조사하면서 그와 관련된 환경을 구성하기위해 (rbenv)[https://github.com/rbenv/rbenv]를 이용하여  

ruby 버전을 맞추고 그러한 작업으로 인해 ruby, gem 관련된것들을 많이 배운거같다.

**rbenv** 는 brew를 이용하여 설치하면 된다.

```brew install rbenv ruby-build```  

여튼 많은 삽질을 통해서 결과적으로 jekyll 블로그로 갈아탈수 있게되었다.  