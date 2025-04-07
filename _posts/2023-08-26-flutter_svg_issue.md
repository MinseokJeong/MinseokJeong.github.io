---
layout: post
title:  "Flutter SVG 파일 화면 렌더링 이슈 해결"
date:   2023-08-26 18:30:00 +0900
category: dev
tags: [flutter]
published: false
---

[flutter_svg](https://pub.dev/packages/flutter_svg) 패키지를 이용하여 figma로 생성한 svg 파일이 화면에 보여지지 않는 현상이 발견 되었다.  

[flutter_svg 깃허브 레퍼지토리](https://github.com/dnfield/flutter_svg/tree/master/packages/flutter_svg)에 issues 란을 확인하면 이런 문제가 빈번히 발생하는 것 같다.

그래서 이 해결과정을 공유하고자 글을 남긴다.

(https://github.com/dnfield/flutter_svg/issues/102)

```shell
╰─○ flutter create flutter_motion_sensor_test

```