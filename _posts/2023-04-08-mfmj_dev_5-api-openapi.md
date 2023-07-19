---
layout: post
title: '"돈의 흐름" 웹사이트 개발기 #5'
date: '2023-04-08T18:06:00.000+09:00'
author: MJ
tags: [현재환율API, 한국수출입은행, OpenAPI, dart, flutter, Dev, SideProject]
modified_time: '2023-04-22T19:17:49.281+09:00'
---

돈의 흐름 관련되서 환율정보 역시 추가하려고 한다.

  

환율정보를 공공데이터 포털에서 찾아보니 [한국수출입은행](https://www.koreaexim.go.kr/ir/HPHKIR020M01?apino=2&viewtype=C&searchselect=&searchword=) 에서

  

API를 제공한다고 안내해준다.

  

한국수출입은행 웹사이트에 접속해서 확인해보니 국제금리, 대출금리, 현재환율에 관해서 API로 제공한다고 나와 있다.

  

![](/assets/images/2023/04/08/1.png)

한국수출입은행에서 제공하는 openAPI 목록

사용을 하기 위해서는 공공데이터포탈과 같이 인증키를 발급받아야 한다.

  

인증키 발급은 사용하고자 하는 API를 위에서 선택하고 아래로 스크롤하다 보면

  

인증키 발급 버튼이 나온다.

  

 
![](/assets/images/2023/04/08/2.png)

인증키 발급 신청 버튼 위치 참고

  
인증키를 발급받고 [Open API 개발명세](https://www.koreaexim.go.kr/ir/HPHKIR020M01?apino=4&viewtype=C#tab1)  를 읽고 구현하자.

  

저번과 같이 freezed 패키지를 이용하여 환율관련 모델 클래스를 만들어보자.

``` json
{

"result": 1,

"cur_unit": "AED",

"ttb": "288.78",

"tts": "294.61",

"deal_bas_r": "291.7",

"bkpr": "291",

"yy_efee_r": "0",

"ten_dd_efee_r": "0",

"kftc_bkpr": "291",

"kftc_deal_bas_r": "291.7",

"cur_nm": "아랍에미리트 디르함"

},

```


첨부한 위의 json 데이터는 한국수출입은행 현재환율 API로 query한 데이터 모습이다.

위와 같이 모든 데이터는 문자 형태로 되어있으며, 날짜는 나와 있지 않아서,

위와 동일한 형태의 model클래스를 만들어다.

```dart
(_ExImExchange)

  

class _ExImExchange with _$_ExImExchange {

const factory _ExImExchange({

required int result,

required String cur_unit,

String? ttb,

String? tts,

required String deal_bas_r,

String? bkpr,

String? yy_efee_r,

String? ten_dd_efee_r,

String? kftc_bkpr,

required String kftc_deal_bas_r,

required String cur_nm,

}) = __ExImExchange;

  

factory _ExImExchange.fromJson(Map<String, Object?> json) =>

_$_ExImExchangeFromJson(json);

}

```


  

  
그리고 _ExImExchange 변수와, 날짜정보를 갖는 변수를 갖는 ExImExchange를 만들었다.

  
```dart
//This is wrapper class

class ExImExchange {

final _ExImExchange _data;

final String date;

  

const ExImExchange(this._data, this.date);

ExImExchange.fromJsonWithDateString(Map<String, Object?> json, String date)

: this._data = _ExImExchange.fromJson(json),

this.date = date;

  

Map<String, dynamic> toJson() => {'date': date, ..._data.toJson()};

  

@override

String toString() {

return '${_data.toString()}, date:${date}';

}

  

String get cur_unit => _data.cur_unit;

String? get ttb => _data.ttb;

String? get tts => _data.tts;

String get deal_bas_r => _data.deal_bas_r;

String? get bkpr => _data.bkpr;

String? get yy_efee_r => _data.yy_efee_r;

String? get ten_dd_efee_r => _data.ten_dd_efee_r;

String? get kftc_bkpr => _data.kftc_bkpr;

String get kftc_deal_bas_r => _data.kftc_deal_bas_r;

String get cur_nm => _data.cur_nm;

}
```


  

  

클래스 멤버변수를 보면 어떤것은 ? 형태로 nullable로 표시되어있는것들은,

데이터를 파싱하는 과정에서 nullable로 표시되어있는 것들은 간간히 값이 없는 형태로(null)인 형태로 들어온다. 그래서 예외처리 식으로 저렇게 Nullable로 표시하였다.

  

실제 위와 같은 모델클래스를 가지고 데이터를 저장하려고 하였는데, 한국수출입은행 API는 발급받은 key로는 하루 1000회 호출 제한이 걸려있다.

  

한번 호출할때마다 해당 날짜에 환율 정보만 가져올수 있다.

  

환율데이터가 어느날짜까지 제공하는지 몰라서, 인터넷으로 찾아보다가 하나은행에서 환율정보를 제공하는데 날짜를 선택해서 확인해보니 아래와 같은 문구가 나왔다.

![](/assets/images/2023/04/08/3.png)

하나은행에서 제공하는 호나율조회는 1995년 1월 3일부터 가능하다고 나온다.

하나은행도 한국수출입은행 같은곳에서 환율정보를 받아오는것은 아닐까 생각하고,

한국수출입은행 환율정보 API로도 1995년 1월 3일 이전 데이터를 시험으로 조회 해보니 비어있는 값을 주는것을 확인했다.

그래서 최소 호출 날짜는 1995년 1월 3일로 해서 오늘 날짜까지 하면은 일단 일수로만 따져도 1천일은 우습게 넘어간다.

  

실제 호출횟수 1천번을 넘어가면 보내주는 데이터의 result 값이 4로 나오고 어떤 데이터도 제공해주지 않는다.

  

이와 같은 상황에서 나는 하루만에 다 모든 정보를 가지고 싶어서, 인증키를 사용하다가 호출횟수 초과가 나오면 다시 인증키를 발급받아서 사용하는 방식으로 했다.

그렇게 여러번 반복작업을 통해 제공하는 모든 날짜의 환율 데이터를 가져올수 있었다.

![](/assets/images/2023/04/08/4.png)

인증키 발급후 재발급 반복작업으로 인한 내역...

  

실제 서버에 저장할때는 숫자들 같은경우에는 문자 형태가 아닌 숫자형태로 변환해서

데이터베이스에 저장하였다.


![](/assets/images/2023/04/08/5.png)

서버에 저장한 환율정보