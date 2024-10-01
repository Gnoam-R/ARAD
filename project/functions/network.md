## 🔍 network
서버에 연결하기 위해서 `http` 기반 통신 인터페이스(API 가이드)를 바탕으로 개발을 진행 했습니다.

통신 프로토콜은 `REST API` 기반으로 `JSON` 데이터를 교환하는 방식을 선택 했습니다.

안드로이드는 `Retrofit2`(http 통신 함수가 있는 간편 라이브러리)라는 `Jetpack` 라이브러리를 활용하여 네트워크 통신을 구현하게 되었습니다.

### API 종류

<img src="https://github.com/user-attachments/assets/ed0071e4-b097-444a-b4c0-5a128eb0da9b" width="50%" height="80%" />
![image]()


## 📝 Feature Check List
  - [x] API 가이드 확인 및 연결 작업
  - [x] Repository 패턴을 적용하여 코드 재 사용 가능한 코드 구현
  - [x] 이미지, POI 데이터 서버 업로드 및 사용자 정보 수신 
  - [x] 원활한 시나리오 flow 구동을 위해 callback, 코루틴 사용

## 📷 Screenshot

<!-- 작업한 화면이 있다면 스크린 샷으로 첨부해주세요. -->

<h1 align="center">

|  AR 이미지 업로드   |  업로드 요청   |  이미지 승인 or 반려 |
| :-------------: | :-------------: | :-------------: |
| <img src="https://github.com/user-attachments/assets/c8007af9-9fde-405e-b34f-0dcb2ee2c85c" width="200" height="450"/> | <img src="https://github.com/user-attachments/assets/b6182c6f-09e7-404d-b03f-5233b9a90eab" width="200" height="450"/> | <img src="https://github.com/user-attachments/assets/7d455f94-cc37-40eb-90f4-47631dd33dc6" width="250" height="450"/> | 
 

|  업로드 정보  |
| :-------------: |
<img src="https://github.com/user-attachments/assets/de9ef169-b9c0-4e7e-a305-94f108f5f7e9" width="600" height="450"/> | 

</h1>

## 📮 관련 이슈

### 네트워크 flow 처리 및 유지보수

- 싱글톤으로 서버와 통신할 수 있는 네트워크 클래스를 개발하여 단일 인터페이스를 사용하도록 구현하였고 네트워크 통신시 발생하는 시나리오 flow적인 오류에 대해서 고민하게 되었습니다.


### AR 이미지 데이터 업로드

- 문제 flow: UAAL 앱 실행 -> 이미지 촬영 및 데이터 전달(to Android App) -> Android App 이미지 수신 및 서버 연결 -> image & POI 데이터 업로드
- Unity 앱이 종료되고 이미지 데이터를 서버에 업로드 하기 때문에 위 flow는 수정해야 되었습니다. -> 요구 사항은 Unity 앱이 켜진 상태로 서버에 데이터 업로드
- 결과: Unity App 실행시 Android App의 특정 클래스에 접근하여 실행시키는 방법을 찾아내어 해당 방법으로 이미지 데이터 업로드 flow를 작성함

### 메인 홈 화면에서 네트워크 flow 오류

- 메인 화면 실행시 네트워크 서버에서는 카카오 혹은 구글 소셜 프로필 이미지와 서버에 등록된 POI 데이터를 가져옵니다.

- 그 이후 구글 맵을 연동해 전달받은 POI의 위치 정보를 바탕으로 Maker를 찍게 되는데 기존 coroutine 방식으로 진행시 카카오 정보와 서버로부터 POI 데이터를 가져오는 사이 Google Map이 실행되는 문제

- 소셜 프로필 이미지와 POI를 구글 맵 API 마커에 연결하는 작업시 데이터를 불러오는 과정에서 실패되어 앱이 종료되는 이슈가 있었습니다.

<br/>

- 결과: 
    - Coroutine보다는 CallBack을 사용해 A시나리오(네트워크 통신, 카카오 API)이 종료되지 않으면 B시나리오(구글 맵)이 실행되지 않는 flow로 수정(Coroutine 사용법 미숙으로 인해서)
    - Google Map Marker는 네트워크 통신이 정상적으로 동작 안될시에 대한 처리를 수행
        - 기본 이미지 및 POI 정보를 마커에 등록하여 네트워크 통신이 완료되지 않더라도 배치가 될 수 있도록

<br/>

<div align=center> 

|  구글 맵 Marker & POI 연동  |
| :-------------: |
<img src="https://github.com/user-attachments/assets/a8ebf5cd-e56a-4604-b47d-1b609faab578" width="200" height="450"/> | 

<div/> 
