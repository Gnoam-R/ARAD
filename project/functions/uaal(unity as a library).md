## 🔍 UAAL (Unity as a Library)

`UAAL`은 안드로이드 앱을 포함하는 다양한 플랫폼에서 증강 현실 기술을 개발하기 위한 프레임워크 이며, 이 라이브러리를 활용하면 AR 이미지 혹은 화면 그 자체를 생성 및 관리하는 API를 제공 받을 수 있습니다.

ARAD에서는 AR 이미지 생성과 촬형 화면을 UAAL을 통해서 진행하였고 이를 서버에 업로드하는 기능을 구현하게 되었습니다.

[Gnoam 블로그 - Unity 모듈 연동](https://velog.io/@romin1027/안드로이드에서-Uaal-활용1)

<br/>
[Unity 앱 개발자 - 신은지님 ]
(https://github.com/Eunji-new)

## 📝 Feature Check List
  - [x] UAAL 설정 및 적용
  - [x] ARAD(Android) <-> UAAL(Unity 앱) 데이터 송 수신
  - [x] ARAD Sever(AnyRactive 자체 서버)에 이미지 데이터 업로드

## 📷 Screenshot

<!-- 작업한 화면이 있다면 스크린 샷으로 첨부해주세요. -->

<h1 align="center">

|   UAAL 앱1  |   UAAL 앱2   |
| :-------------: | :-------------: |
| <img src="https://github.com/user-attachments/assets/ea1ee3e1-5aab-4933-8fec-63f523134552" width="200" height="450"/> | <img src="https://github.com/user-attachments/assets/16da1ece-2f25-4d59-9e80-4f888d414b73" width="200" height="450"/> | 


</h1>

## 📮 관련 이슈

### UAAL 설정
ARAD에 UAAL을 적용하려고 진짜 정~~~~말 수 많은 setting을 진행 해야 되었는데 시간을 갈아 넣어 고쳐야 하는 설정들을 정리 했습니다.
> 그리고 UAAl을 업데이트 하는 경우(Unity에서 새롭게 build 하는 경우) 기존 설정이 초기화 되어 다시 적용해야 되었다...

ARAD의 경우 아래 작업을 수정 및 적용하면 정상적으로 앱이 실행되는 것을 확인 했습니다. 

[UAAL Setting 방법](https://velog.io/@romin1027/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C%EC%97%90%EC%84%9C-Uaal-%ED%99%9C%EC%9A%A93)

1. app 내부 build.gradle 설정
2. gradle.properties 수정
3. UnityLibrary 의 build.gradle 파일을 수정해준다.
4. string.xml 추가
5. Intent Filter 수정
6. ndk 설치 및 경로 추가
7. setting.grandle 파일에서 유니티 추가
8. android:process(:Unity) 추가

### Android & Unity 앱 간의 데이터 송 수신 문제
두 앱간의 통신을 위해서 여러 가지 방법을 사용해 보았고 결론적으로 부적합 하다고 판단했습니다.
- UserPreference, Room : ARAD앱과 Unity앱은 각각의 Local DB가 존재 함. 즉 2개의 앱은 서리의 데이터를 공유할 수 없습니다.
- ContentProvider : AR 이미지, POI(위치 정보)를 저장하기 위해 해당 방식은 꾸준한 관리(데이터 삭제, 호출, 저장)가 필요 합니다. 

ARAD앱에서 Unity 앱을 실행하게 될 때 Intent로 앱을 실행하게 되는데 이떄 전달하고자 하는 데이터를 UserPreference로 일시 저장 그리고 Intent의 파라미터로 전달하는 방식을 수행 했습니다.

### Android앱에 UAAL(Unity 앱)이 추가로 인한 앱의 용량 제한


