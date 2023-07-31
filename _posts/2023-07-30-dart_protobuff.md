---
layout: post
title:  "Dart Protobuff 사용기"
date:   2023-07-30 18:30:00 +0900
category: dev
---

[Protocol Buffer 또는 protobuff](https://protobuf.dev/reference/dart/dart-generated/)라고 불리는 데이터 포맷이 있다.

[json](https://www.json.org/json-en.html) 형식이 가장 일반적으로 많이 사용되는 데이터 형식이고 protobuff는 구글에서 만들어서 내부적으로 사용하다가 오픈소스로 나오게된 데이터 형식이다.  

가장 큰 차이점 하나만을 이야기 하자면, json은 텍스트 형식으로 json파일을 열었을때 텍스트 plain text이기 때문에 사람이 바로 알아 볼수 있다.  

이와 반대로 protobuff는 바이너리 데이터로 파일을 열어도 텍스트 형식이 아니기때문에, 파일을 다시 의미 있는 데이터형식으로 parsing 하고 그후 데이터를 사람이 읽을 수있도록 후처리를 해야 사람이 눈으로 읽을수가 있다.  

이렇게 차이가 있음에도 protobuff의 장점은 json 대비 작은 용량을 가지며 네트워크 트래픽을 줄일수 있고 데이터 사이즈가 작기떄문에 대용량 데이터 전송에 적합한 이유가 장점인 것 같다.  

일단 구글에서도 공식적으로 현재 대부분의 구글 사내 데이터 형식으로 protobufff를 사용하고 있다고 한다.  

protobuff는 [gRPC](https://grpc.io/) 때문에 유명해 진것 같은데, gRPC관련해서는 먼저 protobuff를 사용해보고 나서 하자.

protobuff 사용 관련해서는 다음의 [유튜브 영상](https://www.youtube.com/watch?v=jCbclWBV32o)을 참고 했다.

<br /><br />

# protoc 설치

가장 먼저 해야 할 일은 protoc, 즉 protocolbuffer compiler를 설치 해야 한다.  

[Protocol Buffer Compiler Installation
](https://grpc.io/docs/protoc-installation/) 링크를 참조하여 설치하도록 하자.

터미널을 열고 ```brew install protobuff```를 실행해서 protobuff 컴파일러를 설치하자.

```bash
# protobuf 설치 내역

╰─⠠⠵ brew install protobuf 
Running `brew update --auto-update`...
==> Auto-updated Homebrew!
==> Updated Homebrew from dc2ed9f02 to 4.1.2 (c531a35ae).
Updated 3 taps (homebrew/services, homebrew/core and homebrew/cask).
==> New Formulae
bfs                            llm                            plog                           python-cryptography            terragrunt-atlantis-config
govulncheck                    mvfst                          pop                            quictls                        tzdiff
killport                       pgrok                          prettierd                      sbom-tool
==> New Casks
4k-video-downloaderplus                keyclu                                 replay                                 sfm
herd                                   lm-studio                              ripx                                   showmeyourhotkeys

You have 13 outdated formulae and 1 outdated cask installed.


The 4.1.0 release notes are available on the Homebrew Blog:
  https://brew.sh/blog/4.1.0
The 4.1.2 changelog can be found at:
  https://github.com/Homebrew/brew/releases/tag/4.1.2
protobuf 21.9_1 is already installed but outdated (so it will be upgraded).
==> Fetching dependencies for protobuf: abseil
==> Fetching abseil
==> Downloading https://ghcr.io/v2/homebrew/core/abseil/manifests/20230125.3-1
#################################################################################################################################################### 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/abseil/blobs/sha256:c789ff211d0d81fba211ffa4abc0a0e5ce430698ae0e590057cbeeb73d620d15
#################################################################################################################################################### 100.0%
==> Fetching protobuf
==> Downloading https://ghcr.io/v2/homebrew/core/protobuf/manifests/23.4
#################################################################################################################################################### 100.0%
==> Downloading https://ghcr.io/v2/homebrew/core/protobuf/blobs/sha256:eb86d06abace7c985b21ccac17ea418131e17689c9fdeddbcaa998d7088c89de
#################################################################################################################################################### 100.0%
==> Upgrading protobuf
  21.9_1 -> 23.4 

==> Installing dependencies for protobuf: abseil
==> Installing protobuf dependency: abseil
==> Pouring abseil--20230125.3.arm64_ventura.bottle.1.tar.gz
🍺  /opt/homebrew/Cellar/abseil/20230125.3: 723 files, 10.6MB
==> Installing protobuf
==> Pouring protobuf--23.4.arm64_ventura.bottle.tar.gz
==> Caveats
Emacs Lisp files have been installed to:
  /opt/homebrew/share/emacs/site-lisp/protobuf
==> Summary
🍺  /opt/homebrew/Cellar/protobuf/23.4: 389 files, 12.2MB
==> Running `brew cleanup protobuf`...
Disable this behaviour by setting HOMEBREW_NO_INSTALL_CLEANUP.
Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
Removing: /opt/homebrew/Cellar/protobuf/21.9_1... (333 files, 20.4MB)
==> Caveats
==> protobuf
Emacs Lisp files have been installed to:
  /opt/homebrew/share/emacs/site-lisp/protobuf
```

<br />  

정상적으로 설치되어 있는지 확인해보자.

```shell
# protoc --version 명령어를 이용해서 버전을 확인함과 동시에 실행이 된다면 정상적으로 설치되었 다는 것을 알 수 있다.
╰─⠠⠵ protoc --version
libprotoc 23.4
```  

이후 protoc에서 dart를 사용할 수 있도록 아래와 같이 명령어를 입력한다. ([참조](https://pub.dev/packages/protoc_plugin))
``` shell
dart pub global activate protoc_plugin
```


<br />  

# protobuff 테스트 프로젝트 생성

이제 protobuff 를 시험해볼 수있게 다트 콘솔 프로젝트를 하나 만들자.

```shell
#protobuff 테스트용 다트 프로젝트 생성
╰─○ dart create --template=console protobuff_test_dart
Creating protobuff_test_dart using template console...

  .gitignore
  analysis_options.yaml
  CHANGELOG.md
  pubspec.yaml
  README.md
  bin/protobuff_test_dart.dart
  lib/protobuff_test_dart.dart
  test/protobuff_test_dart_test.dart

Running pub get...                     3.0s
  Resolving dependencies...
  Changed 47 dependencies!

Created project protobuff_test_dart in protobuff_test_dart! In order to get started, run the following commands:

  cd protobuff_test_dart
  dart run
```

<br />

vscode를 이용하여 생선한 프로젝트를 열어주고 proto 파일을 하나 작성 해보자.

```proto
//test_message.proto
syntax = "proto3";
package mj;

message TestMessage{
    int64 messageId = 1;
    bool importantMessage = 2;
    string texts = 3;
}
```

내용은 그냥 정수 하나와, 불리언형 변수 하나, 그리고 문자열 이렇게 3가지 필드가 있다.

이렇게 작성된 proto파일은 **protoc** 컴파일러를 이용해서 사용하고자 하는 언어로 코드를 만들어주면 그것을 이용해서 사용하는 방식이다.  

protoc로 변환된 코드를 담아줄 proto_dart 라는 폴더를 만들어 준다.

```shell
mkdir proto_dart
```

이제 위에서 작석한 proto 파일을 protoc를 이용해 컴파일 해보자.

```shell
protoc  --dart_out=./proto_dart/ test_message.proto
```

protoc로 컴파일된 dart 코드는 아래와 같이 여러가지가 나오는것을 알수 있다.  

저기서 [파일명].pb.dart 라고 되어있는 파일을 import 해서 사용하면 된다!

![](/assets/images/2023/07/30/1.png)


# dart protobuff 테스트!

이제 dart를 이용해서 protoc를 이용해 만들어진 proto 파일을 사용해보자.

먼저 [pub.dev](https://pub.dev)에서 [protobuf](https://pub.dev/packages/protobuf) 패키지를 프로젝트에 추가하자.

``` shell
╰─⠠⠵ dart pub add protobuf
```

그리고 테스트 코드를 작성하자.

``` dart
import 'package:protobuff_test_dart/model/out/test_message.pb.dart';

void main(List<String> arguments) {
  TestMessage testMessage = TestMessage();

  testMessage.messageId = Int64(777);
  testMessage.importantMessage = true;
  testMessage.texts = 'Test protobuff!';

  print(testMessage);
}

//디버그 콘솔 출력 결과
messageId: 777
importantMessage: true
texts: Test protobuff!
```

동작이 되는 것을 확인할 수 있다.

# json과 protobuff 데이터 사이즈 비교!

TestMessage를 랜덤으로 생성하도록 하고 반복문을 돌면서,  

총 protobuff 일때의 바이트 크기와, json일때의 바이트 크기를 비교해서 얼마나 줄어 드는지 확인을 해보았다.


``` dart
void test_TestMessage() {
  print('Protobuff contains number and string');
  int totalProtobuffBytes = 0;
  int totalJsonBytes = 0;

  final randomGenerator = Random.secure();

  final iterationCount = randomGenerator.nextInt(100000) + 1;
  print('Loop iteration ${iterationCount}');
  for (int i = 0; i < iterationCount; ++i) {
    if (i % 1000 == 0) {
      print('loop: ${i}');
    }
    final testMessage = createRandomTestMessage();

    final testMessageBytes = testMessage.writeToBuffer();
    totalProtobuffBytes += testMessageBytes.length;

    final jsonFormatString = testMessage.toProto3Json().toString();
    final jsonFormatStringBytes = utf8.encode(jsonFormatString);
    totalJsonBytes += jsonFormatStringBytes.length;
  }
  print('Total Iteration Count: ${iterationCount}');
  print('Total ProtoBuff Data Bytes: ${totalProtobuffBytes}');
  print('Total JSON Data Bytes: ${totalJsonBytes}');
  print(
      'Proto Buff is lighter than json approximately ${((1.0 - (totalProtobuffBytes / totalJsonBytes)) * 100.0).toStringAsFixed(1)}% reduced.');
}

//디버그 콘솔 출력 결과
Total Iteration Count: 53706
Total ProtoBuff Data Bytes: 21958097
Total JSON Data Bytes: 26022213
totalProtobuffBytes / totalJsonBytes = 0.84
```

테스트 결과는 protobuff가 약 json 포맷 대비 약 16% 정도 감소 된것으로 나타났다.  

필드 수가 별로 없고, 문자열이다 보니까 드라마틱하게 큰차이는 보이지 않는다.  

그래서 문자열은 빼고 숫자들로만 구성한 테스트 프로토 메시지를 작성해 보았다.

``` proto
message TestMessageOnlyHaveNumber{
    repeated int64 number1 = 1;
    repeated double number2 = 2;
}
```

여기서 repeated 의 뜻은 반복된다는 뜻으로 쉽게 배열이라고 생각하면 된다.

이렇게 int64형과, double 형 배열을 생성해서, 배열의 사이즈도 랜덤하게 생성해서 데이터 사이즈 크기 차이를 한번 비교를 해보았다.


``` dart
void test_TestMessageOnlyHaveNumber() {
  print('Protobuff only contains number');
  int totalProtobuffBytes = 0;
  int totalJsonBytes = 0;

  final randomGenerator = Random.secure();

  final iterationCount = randomGenerator.nextInt(9999) + 1;

  print('Loop iteration ${iterationCount}');
  for (int i = 0; i < iterationCount; ++i) {
    if (i % 1000 == 0) {
      print('loop: ${i}');
    }
    final testMessage = createRandomTestMessageOnlyHaveNumber();

    final testMessageBytes = testMessage.writeToBuffer();
    totalProtobuffBytes += testMessageBytes.length;

    final jsonFormatString = testMessage.toProto3Json().toString();
    final jsonFormatStringBytes = utf8.encode(jsonFormatString);

    totalJsonBytes += jsonFormatStringBytes.length;
  }
  print('Total Iteration Count: ${iterationCount}');
  print('Total ProtoBuff Data Bytes: ${totalProtobuffBytes}');
  print('Total JSON Data Bytes: ${totalJsonBytes}');
  print(
      'totalProtobuffBytes / totalJsonBytes = ${((totalProtobuffBytes / totalJsonBytes)).toStringAsFixed(2)}');
}


//디버그 콘솔 출력 결과
Total Iteration Count: 9705
Total ProtoBuff Data Bytes: 8452457
Total JSON Data Bytes: 19671966
totalProtobuffBytes / totalJsonBytes = 0.43
```

결과를 보면 protobuff의 사이즈는 json형식일때의 데이터의 43% 정도 밖에 안되는 크기로 무려 57%정도가 줄어 들었다.


일단 이렇게해서 protobuff 사용기를 후다닥 마무리 하겠다.


## 결론은 네트워크를 통해 데이터를 전송할때 protobuff 사용을 고려하면 네트워크 트래픽을 줄여 트래픽도 다 돈인데 돈을 아낄수 있으며 더적은 용량으로 보내기때문에 protobuff로 저장된 데이터를 보낸다고 할때 이득이 있겠다.  

```js
main(void){
    int a = 0;
}
```