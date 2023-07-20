---
layout: post
title: '"돈의 흐름" 웹사이트 개발기 #4'
date: '2023-04-05T18:07:00.002+09:00'
author: MJ
tags: [freezed, json_serializable, dart, flutter]
modified_time: '2023-04-05T18:07:37.938+09:00'
category: dev
---
 금가격을 추가 했으니, 석유가격도 서버에 저장하는 기능을 구현해보자.

먼저 석유 가격 관련 model 클래스를 만드는데, 무작정 만드는것보다,

dart 의 [freezed](https://pub.dev/packages/freezed) 패키지를 이용해서 쉽게 Model 클래스를 만들고, toJson, toMap 함수를 자동으로 만들어 주도록 만들어보자.

  

현재 mfmj_shared 라는 프로젝트는 dart 프로젝트다, flutter 프로젝트가 아니기때문에, dart project 설치 가이드라인을 따른다.

![](/assets/images/2023/04/05/1.png)

freezed 패키지 install 방법

  

나는 fromJson/toJson 과 같은 함수도 필요하기 때문에

"dart pub add json_annotation", "dart pub add --dev json_serializable",

두 명령어도 입력했다.

  

  

패키지 가이드라인에 따라 첫 코드 시작은 아래와 같이 시작한다.

  

"oil_price.dart" 라는 파일을 생성했다.

```dart
import 'package:freezed_annotation/freezed_annotation.dart';
part 'oil_price.freezed.dart';
```
```part 'oil_price.freezed.dart';``` 에서 알수 있듯이 내가 어느정도 모양만 갖추면은,

현재 존재하지 않는 "oil_price.freezed.dart" 파일을 freezed 패키지가 만들어주고,

"oil_price.dart" 파일은 자동생성된 파일과 합쳐져 완성된 코드가 되는것이다.

  

아래 코드는 공공데이터 포털에서 가져올 석유가격 데이터를 모델링한 클래스다.

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

  

//Below code is required for freezed package

part 'gov_oil_price.freezed.dart';

  

//Below code is required for generate serializable package

part 'gov_oil_price.g.dart';

  

@freezed

class GovOilPrice with _$GovOilPrice {

const factory GovOilPrice({

required String basDt,

required String oilCtg,

required String wtAvgPrcCptn,

required String wtAvgPrcDisc,

required String trqu,

required String trPrc,

}) = _GovOilPrice;

  

factory GovOilPrice.fromJson(Map<String, Object?> json) =>

_$GovOilPriceFromJson(json);

}

```



중요한것은 생성자의 파라미터 이름들이 실제 송신,수신 받을 json 형태의 데이터들 각각의 key 가 된다는 것이다.

아래는 실제로 공공데이터포털에서 수신받은 json 포맷의 데이터다.

```json
{

"basDt": "20230330",

"oilCtg": "경유",

"wtAvgPrcCptn": "1358.83",

"wtAvgPrcDisc": "1373.83",

"trqu": "13510000",

"trPrc": "18514200000"

},
```



편리하게도 fromJson만 해두어도 toJson 까지 자동적으로 만들어준다는 편리함은 있다.

현재 되돌아보니 내가 필요한것은 json데이터를 파싱하는게 귀찮아서 freezed와 같은 패키지를 이용해 좀더 쉽게 해보자는 것이였는데, 나한테 딱 필요한것은 freezed보다는 json_serializable 패키지면 충분하다.

  

GovOilPrice클래스의 factory 생성자와 fromJson 생성자만을 모양만 만들어두고,

"dart run build_runner build" 명령어를 사용하면 자동적으로 파일을 생성해준다.

![](/assets/images/2023/04/05/2.png)

```shell
dart run build_runner build
```

  
![](/assets/images/2023/04/05/3.png)

자동으로 생성된 파일을 확인할 수 있다.