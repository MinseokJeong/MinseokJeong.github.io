---
layout: post
title:  "프로그래머스 코딩테스트 C++ Visual Studio Code 개발 환경 설정"
date:   2023-07-29 18:30:00 +0900
category: dev
---

어제 [프로그래머스](https://programmers.co.kr/)를 통해 코딩테스트를 볼 당시  

vscode로 내 코드의 문법이라던지 동작은 하는지 확인 하는 작업을 진행했었다.

그당시의 상황에 vscode c++ 환경을 프로그래머스의 환경하고 동일하게 구성하는 작업에  

대해서 이야기 하고자 한다.  

# 프로그래머스 C++ 컴파일 옵션


![프로그래머스 컴파일 옵션](/assets/images/2023/07/29/1.png)

의의 그림에서 보는바와 같이 C++ 컴파일 옵션을 설정해야 동일한 환경이라고 할 수 있다.  

![프로그래머스 컴파일 옵션](/assets/images/2023/07/29/2.png)
프로그래머스에 코딩테스트를 볼때 위의 그림과 같이 상단의 컴파일 옵션을 누르면 확인 할 수 있다.

컴파일러로는 **clang++** 를 사용하며 C++17을 사용한다고 되어있다.

현재 내 컴퓨터에 clang++ 컴파일러가 설치 되어있는지 확인 해볼 필요가 있다.

현재 내 컴퓨터는 mac이므로 mac에서의 설정을 이야기 해보도록 하겠다.

# clang++ 컴파일러 존재 확인

터미널을 열고 ```clang++ -v```을 입력해 보자.  

```shell
╰─⠠⠵ clang++ -v
Apple clang version 14.0.3 (clang-1403.0.22.14.1)
Target: arm64-apple-darwin22.5.0
Thread model: posix
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin
```

mac에서는 다행이도 clang++가 기본적으로 설치가 되어있는 것인지,  

아니면 여러 프로그램들을 설치하다 보니 자연스럽게 설치가 되어있는건지는  

모르지만 일단 설치가 되어있는것을 확인 할 수 있었고,  

프로그래머스에서는 **clang++ 의 버전이 10.0** 을 사용 하고 있는데  

보통 상위 버전의 컴파일러는 하위버전은 호환이 되게 설계하지 않을까  

추측으로 동일한 버전으로 맟추는게 제일 좋지만,  

그냥 상위버전 호환을 추측하고 사용하기로 결정.  

**일단 10.0이상은 다 동작할것이다 라는 가정**을 안고 갈것이다.  


# clang++ 위치 찾기
```shell
╰─⠠⠵ which clang++
/usr/bin/clang++
```
터미널을 열고 which 커맨드를 이용하여 clang++ 프로그램의 위치를 찾았다.
프로그램 경로 **/usr/bin/clang++** 를 기억하자.

# vscode 설정

먼저 아무 디렉토리나 만든후 vscode를 열어서 간단히 hello world 를 출력하는 c++ 코드를 작성 하자.

```c++
#include <iostream>

using namespace std;

int main(int argc, char* argv[])
{    
    cout << "HELLO WORLD!" << endl;        
    return 0;
}
```

<br />

이렇게 작성 후에 **command palette**를 열어 아래 그림과 같이 C/C++ 구성편집(JSON)을 클릭하도록 하자. 

![C/C++ 구성편집](/assets/images/2023/07/29/3.png)

위의 그림처럼 C/C++ 구성편집을 클릭 후에는 현재 작업 디렉토리의 **.vscode** 폴더 아래에는 *c_cpp_properties.json* 파일이 생성 되어 있을 것이다.  

<br /><br />


그다음 작업으로는 아래 그림처럼 command palette를 열고 C/C++ 디버그 구성추가를 클릭하도록 하자.

![C/C++ 디버그 구성 추가](/assets/images/2023/07/29/4.png)  

위의 그림처럼 작업하고 나면은 .vscode 폴더 아래에  *tasks.json* 파일이 생성 되어 있을 것이다.  

여기서 중요한것은 tasks.json 파일을 열어 위의 프로그래머스에서 컴파일러 옵션과 동일하게 우리도 컴파일러 옵션을 설정 해야 되는 것이다.

프로그래머스에서 설정한 컴파일 옵션은 아래와 같다.  

```calng++ -std=c++17 -O2 -Wno-unused-result -o FILENAME```

컴파일 옵션들의 세부 설명은 ```man clang++``` 또는 ```clang++ -help``` 를 터미널에 입력해서 확인 할수 있다.

```shell
#Information from man clang++
   Language Selection and Mode Options
       -std=<standard>
              Specify the language standard to compile for.
   
   Code Generation Options          
                 -O2 Moderate level of optimization which enables most optimizations.        

#Information form clang++ -help
   -W<warning>             Enable the specified warning

```

tasks.json 파일을 열고 아래와 같이 args 속성에 프로그래머스의 설정과 같이 컴파일 옵션을 추가한다.

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: clang++ 활성 파일 빌드",
            "command": "/usr/bin/clang++",
            "args": [
                "-fcolor-diagnostics",
                "-fansi-escape-codes",
                "-g",
                "-std=c++17",
                "-O2",
                "-Wno-unused-result",
                "${file}",
                "-o",
                "${fileDirname}/${fileBasenameNoExtension}"
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": "build",
            "detail": "디버거에서 생성된 작업입니다."
        }
    ],
    "version": "2.0.0"
}
```

### tasks.json 파일 안에 compiler 옵션만 추가 해주는게 제일 중요하다!

<br /><br />



# C++ 코드 실행하기

이제 다시 코드 cpp 파일을 열고 아래 그림과 같이 상단의  

빨간색 동그라미로 그려진 디버깅 버튼을 클릭하면 디버깅 구성 선택이 있고,  

그중 clang++ 활성 파일 빌드를 선택해주면 프로그램이 동작하는 것을 볼 수 있다.

![C/C++ 구성편집](/assets/images/2023/07/29/5.png)

vscode는 내가 알기로는 현재 작업디렉토리의 .vscode 폴더내에 launch.json 파일을 보고,  

그다음 현재 프로젝트를 실행하던지 하는데 이미 C/C++에 대해서는 launch.json 파일을 vscode 자체적으로 갖고 있는것 같다. 그래서 자동으로 방금전에 작성한 tasks.json 안에 명시한 태스크를 실행하여 코드를 컴파일 하고 프로그램 실행파일이 나오면 그 나온 결과물을 lldb와 같은 디버깅 전용 프로그램으로 결과물을 로드해서 vscode와 연동하여 디버깅이 가능하게 되는 것 같다. 

<br /><br />

방금전의 내용은 그렇게 중요한것은 아니니 무시하고,  

일단 이렇게 프로그래머스의 C/C++ 코딩테스트 환경을 구성해 보았다.







