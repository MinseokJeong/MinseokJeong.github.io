---
layout: post
title: '급똥 공공화장실 지도 앱 개발기 #2 (대똥여지도)'
date: '2023-03-12T17:50:00.000+09:00'
author: MJ
tags: [flutter, dart, naver map]
modified_time: '2023-03-12T17:50:43.495+09:00'
category: dev
---
현재 모의 위치(세종시립도서관) 중심으로 반경 1km이내의 화장실을 화면상에 나타 냈다. Flutter naver map 관련해서는

[flutter_naver_map](https://pub.dev/packages/flutter_naver_map) 패키지를 사용함.
![](/assets/images/2023/03/12/1.png)

이제 실제 위의 마커를 클릭했을때, 관련된 화장실 정보를 모달 형식으로 띄워보려 한다.  

그런대 flutter naver map 의 dev_preview 라는 개발 버전으로 개발하다보니, 아직 문서가 없는 버전이다보니, 내가 원하는 기능인 마커를 탭할때 호출되는 함수를 찾느라고 조금 애를 먹었다. 

마커를 탭할때 터미널 디버그창에 그래도 탭한 마커의 정보가 출력이 되어서, 관련 기능은 있는걸 확인했고, 마커의 정보가 출력이 되는 그부분을 찾아 찾아서 결국 찾긴 했다.  


일단 현재위치를 나타내는 심볼혹은 마커를 그려봐야겠다. [icons8.com](http://icons8.com) 에서 찾은 아이콘. 

![](/assets/images/2023/03/12/2.png)
위의 현재 위치 이미지를 가지고 지도상에 표시. 

![](/assets/images/2023/03/12/3.png)
현재위치 마커 표시 화장실을 표시하는 마커로는 아래의 이미지를 사용하기로 결정 

![](/assets/images/2023/03/12/10.png)  
현재위치를 중심으로 주변 상하좌우 2KM, 즉 한변이 4KM인 사각형의 지역의 화장실 정보를 표시하도록 하였다.  

![](/assets/images/2023/03/12/11.png)  
현재위치 마커와 화장실 마커 표시 이제 사용자가 손으로 맵을 움직일때마다 현재 보이는 지도 화면의 중심으로 부터 다시 화장실 정보를 찾아 표시하도록 기능을 추가하였다.  

[flutter_naver_map](https://pub.dev/packages/flutter_naver_map) 에서 제공하는 NaverMap 클래스의 파라미터중  onCameraChange에 위기능을 구현하였다.  

구현해보니 문제가 지도를 움직이는동안 너무 많이 호출이 되다보니 성능저하 및 findNearToiletInfo 함수로 주변 화장실정볼를 찾고 다시 도시하도록 구현하였는데, 비동기다보니 findNearToiletInfo가 여러번 호출이 되다보니 같은 변수에 동시에 접근하여 비정상적인 동작이 발생하는것을 확인하였다.  

![](/assets/images/2023/03/12/12.png)  
onCameraChange 함수 원형  

![](/assets/images/2023/03/12/13.png)  
findNearToiletInfo 함수 이와 같은 문제를 해결하기 위해 첫번째 너무많이 호출되는 문제는 사용자가 지도조작이 다끝난 후에 호출되는 onCameraIdle 함수를 사용하여 해결.  

![](/assets/images/2023/03/12/14.png)  

또한 findNearToiletInfo 와 같은 비동기 함수를 여러번 호출 못하도록 강제하도록, Lock 메카니즘을 제공하는 [synchronized](https://pub.dev/packages/synchronized) 패키지를 사용하여 상호배제 시킴으로써 해결 

![](/assets/images/2023/03/12/9.png)  

위와 같이 구현을 통하여 사용자의 지도 움직임이 끝나면 해당지역으로 화장실 정보를 업데이트하도록 기능을 구현하였다. 


![](/assets/images/2023/03/12/8.gif)  

사용자 제스쳐 움직임이 종료되면 화장실 정보 업데이트 화장실 마커를 클릭하면 선택된 화장실의 정보를 새로운 다이얼로그 창을 띄워 화면에 표시하는 기능도 추가 

![](/assets/images/2023/03/12/7.gif)  

화장실 마커를 클릭시 화장실 관련 정보를 새로운 다이얼로그 창에 나타내도록 구현 주소옆에 복사 아이콘을 둬서 복사 아이콘을 클릭하면 해당 주소를 클립보드에 복사하는 기능도 추가

![](/assets/images/2023/03/12/6.gif)  

클립보드로 주소 복사 전화번호를 누르면 전화번호 앱으로 바로 전화를 걸수 있는 기능 추가.  

![](/assets/images/2023/03/12/5.gif)  

전화번호 클릭시 바로 전화를 할수 있는 기능 추가 네비게이션 아이콘 클릭시 네이버 맵을 호출해서 바로 길찾기가 가능하도록 하는 기능 추가. 

이와 같은 기능은 [네이버 URL SCHEME 관련 문서](https://guide.ncloud-docs.com/docs/naveropenapiv3-maps-url-scheme-url-scheme)를 참조 함.(only works when naver map application is installed on user mobile)  

![](/assets/images/2023/03/12/4.gif)  
네이버 지도앱으로 연동 다음 작업은 현재의 밋밋한 UI를 바꾸는 작업을 하면 되지 않을까?