---
layout: post
title:  "Flutter Method/Event Channel 을 이용하여 Platform-specific code 작성 방법"
date:   2023-08-01 18:30:00 +0900
category: dev
published: false
---

Flutter는 UI Framework이다. 그이상 그이하도 아닌 사용자 UI를 정말 쉽게 만들어주는 Framework 이다.

안드로이드/iOS 네이티브 기능들을 이용하려면 [pub.dev](https://pub.dev)에서 사용자들이 만들어둔 패키지를 이용하거나 직접 네이티브 코드를 작성하여 Flutter와 연결 해주어야 한다.  

플루터 공식 홈페이지에 [사용자 정의 플랫폼별 코드 작성](https://docs.flutter.dev/platform-integration/platform-channels)에 관해 자세하게 설명이 나와있으니 참조하면된다.


간단하게 핸드폰의 동작센서(Motion)를 이용하여 핸드폰을 기울이면 기울림에 따라,
화면의 텍스트를 기울이는 간단한 작업을 해봄으로서 각 안드로이드/iOS의 Platform/Event Channel에 대해서 알아보도록 하자.


[안드로이드 동작 센서 문서](https://developer.android.com/guide/topics/sensors/sensors_motion?hl=ko)

[iOS 동작 센서 문서](https://developer.apple.com/documentation/coremotion)

```shell
╰─○ flutter create --org=com.mj.test --template=plugin --platforms=android,ios --android-language=kotlin --ios-language=swift --project-name=test_motion_sensor
 test_motion_sensor
Creating project test_motion_sensor...
Resolving dependencies in test_motion_sensor... (1.1s)
Got dependencies in test_motion_sensor.
Resolving dependencies in test_motion_sensor/example...
Got dependencies in test_motion_sensor/example.
Wrote 56 files.

All done!

Your plugin code is in test_motion_sensor/lib/test_motion_sensor.dart.

Your example app code is in test_motion_sensor/example/lib/main.dart.


Host platform code is in the android, ios directories under test_motion_sensor.
To edit platform code in an IDE see https://flutter.dev/developing-packages/#edit-plugin-package.


To add platforms, run `flutter create -t plugin --platforms <platforms> .` under test_motion_sensor.
For more information, see https://flutter.dev/go/plugin-platforms.
```

```shell
╰─○ flutter create flutter_motion_sensor_test

```