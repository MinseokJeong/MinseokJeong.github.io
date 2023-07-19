---
layout: post
title: Callable 클래스 컨셉 in Dart
date: '2023-04-02T14:07:00.001+09:00'
author: MJ
tags:
modified_time: '2023-04-02T14:07:24.645+09:00'
---
Dart shelf 패키지를 이용하여 웹서버를 개발 하는 와중에 궁금증이 생겼다.

![](/assets/images/2023/04/02/1.png)
  
위의 코드를 보면은 Pipeline().addHandler() 코드를 보면은 addHandler 파라미터로 Router 클래스가 들어간다.

하지만 addHandler 함수를 보면 Handler 타입을 인자로 받는다.

![](/assets/images/2023/04/02/2.png)  

Handler 타입은 typedef으로 되어있다. 즉 타입이 펑션(함수)이다.

```dart
typedef Handler = FutureOr<Response> Function(Request request);


@sealed

class Router {

final List<RouterEntry> _routes = [];

final Handler _notFoundHandler;
```

Router 클래스를 보면 일반 클래스와 같이 extends, mixin, with 이런것도 없이,

Router클래스 뿐이다. 그런대 어떻게 addHandler에 Router 클래스를 인자로 넣어 사용할 수 있을까?

결론부터 말하자면 dart 언어의 ["Concept of Callable Classes in Dart"](https://www.geeksforgeeks.org/concept-of-callable-classes-in-dart/) 로 가능하다.

Dart allows the user to create a **callable class** which allows the instance of the class to be called **as a function**. To allow an instance of your Dart class to be called like a function, implement the **call() method**. 

  

위에 적혀있는 글을 보면은 dart는 call() 함수를 재정의 하면 클래스 자체를 함수로 사용할수 있다고 한다.

  

즉 위의 addHandler에 Router 클래스가 인자로 들어가면 Router 클래스의 call() 함수가 들어 가는 것이다.

  
```dart
/// Route incoming requests to registered handlers.

///

/// This method allows a Router instance to be a \[Handler].

Future<Response> call(Request request) async {

    // Note: this is a great place to optimize the implementation by building

    // a trie for faster matching... left as an exercise for the reader :)

    for (var route in _routes) {

        if (route.verb != request.method.toUpperCase() && route.verb != 'ALL') {

            continue;
        }

        var params = route.match('/' + request.url.path);

            if (params != null) {

            final response = await route.invoke(request, params);

                if (response != routeNotFound) {

                return response;

                }

            }

    }

    return _notFoundHandler(request);

}
```

위의 코드는 Router 클래스의 call 구현 부분이다.

  

call 함수는 Request를 인자로 받고 Response를 Future형태로 반환하는 함수다.

  

즉 typedef으로 선언된 Handler의 형태와 일치한다.

  

이렇게 addHandler는 함수를 인자로 받지만, 실제 클래스 오브젝트를 인자로 넣어줄 수 있다는 사실을 알게 되었다.