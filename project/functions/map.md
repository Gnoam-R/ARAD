## 🔍 map(Google)
우선 총 3가지 지도 API(google, kakao, naver)중에서 구글 맵을 선택한 이유를 말씀 드리자면 
- 다양한 API & 개발 자료 보유
    - 카메라 이동 & 각도 조절
    - 마커
    - time zone
    - 등등
- 전 세계적인 데이터베이스 보유
    - ARCore Geospatial API 기반 좌표 데이터를 사용해 여러 나라에서 사용 가능
- 다양한 map 커스텀 기능

> 위 작업들을 수행하기 위해 GPS 권한 설정, Marker 설정을 위해 서버와 데이터 송수신 등 이 내용은 [permission](https://github.com/Gnoam-R/ARAD/blob/main/project/functions/permission.md), [network](https://github.com/Gnoam-R/ARAD/blob/main/project/functions/network.md)를 확인 해주시면 됩니다.

<br/>

## 📝 Feature Check List
  - [x] 구글 map API 구현
  - [x] map 배경 커스텀
  - [x] map Marker bitmap 커스텀 적용
  - [x] POI 위치 데이터 기반(위경도) map Marker 활성화
  - [ ] 현재 나의 위치 to 지하철 위치 & 거리 측정 및 AR Marker 거리 측정

## 📷 Screenshot

<!-- 작업한 화면이 있다면 스크린 샷으로 첨부해주세요. -->

<h1 align="center">

|   구글 맵1   |   구글 맵2   |  구글 맵3 |
| :-------------: | :-------------: | :-------------: |
| <img src="https://github.com/user-attachments/assets/f2f1aea6-041c-4ba5-9d28-5428a8c295b0" width="200" height="450"/> | <img src="https://github.com/user-attachments/assets/f810c3d4-a82b-4ffb-bead-c388fdfcdf58" width="200" height="450"/> | <img src="https://github.com/user-attachments/assets/4ab83bdc-69be-474f-91c7-6b75ea455f38" width="200" height="450"/> |
</h1>

## 📮 관련 이슈

### 구글 맵, 마커 스타일 커스텀 & POI 위치 기반 트래킹

기본 마커와 구글 맵 플랫폼 디자인을 활용하는 것이 아닌 커스텀된 이미지의 디자인이 필요하게 되었습니다.

<br/>

1️⃣ 구글 맵 스타일 커스텀

[Google Map API](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView?authuser=0)를 사용하면 구글에서 제공하는 다양한 API를 사용할 수 있습니다.

 
```kotlin
//GoogleMapRepositoryImpl.kt

// 현재 커스텀 된 구글 맵 View
mGoogleMap.setMapStyle(MapStyleOptions.loadRawResourceStyle(mContext, R.raw.style2_json))

```

`setMapSytle` 에 관한 것은 위 API에서 지도 플랫폼에 [스타일 지정 마법사](https://mapstyle.withgoogle.com/)를 사용해 개발하는 방법을 활용했습니다.
그리고 SnazzyMaps에 들어가면 여러 사람들이 커스텀한 디자인 포맷이 공유되어 있기에 저는 [Subtle Grayscale](https://snazzymaps.com/style/15/subtle-grayscale) 디자인을 선택하여 적용하게 되었습니다.

<br/>

2️⃣ 마커 커스텀

마커에 위치, 제목, 이미지를 추가하여 맵에 적용

```kotlin

mGoogleMap.addMarker(
    MarkerOptions()
        .position(ttukseomStarbucks)
        .title("뚝섬 스타벅스")
        .icon(BitmapDescriptorFactory.fromBitmap(bitmapStarBucks!!))
)
mGoogleMap.addMarker(
    MarkerOptions()
        .position(nearTtukseomStarbucks)
        .title("스타벅스 건너편")
        .icon(BitmapDescriptorFactory.fromBitmap(bitmapStarBucksOpposite!!))
)

bitmapStarBucks = createUserBitmap(BitmapFactory.decodeResource(mActivity.resources, com.example.arad_january.R.drawable.ic_profile_ex2))
bitmapStarBucksOpposite = createUserBitmap(BitmapFactory.decodeResource(mActivity.resources, com.example.arad_january.R.drawable.ic_profile_ex2))
```

<br/>

3️⃣ POI 위 경도 데이터 기반 카메라 이동 및 마킹

서버로 부터 위 경도 데이터를 받아오고 그 후에 `setLastLocation` 코드를 실행 시켰습니다.
MapView onCreate -> 서버와 데이터 송 수신 -> `setLastLocation` 코드 실행 순으로 수행 되어 callBack을 활용하여 해당 flow를 구현했습니다.

```kotlin
class GoogleMapRepositoryImpl () : GoogleMapRepository {
    ...
    override fun setLastLocation(location: Location) {
            val myLocation = LatLng(location.latitude, location.longitude)
            // 현재 나의 위치에 마킹하는 변수
            val marker = MarkerOptions()
                .position(myLocation)
                .title("i am here")
            // 
            val cameraOptions = CameraPosition.Builder()
                .target((myLocation))
                .zoom(18.0f)
                .tilt(45.0f)           // 고도 각도 조절
                .bearing(90f)          // 좌 우 각도 조절
                .build()
    
            val camera = CameraUpdateFactory.newCameraPosition(cameraOptions)
    
            MyLocation = myLocation
            MyCameraOptions = cameraOptions
            MyCamera =  CameraUpdateFactory.newCameraPosition(MyCameraOptions)
    }
    ...
```
[Google Maps Platform - camera & view](https://developers.google.com/maps/documentation/android-sdk/views?hl=ko)

## reference

https://breadboy.tistory.com/282

https://developers.google.com/maps/documentation/get-map-id?hl=ko&authuser=0

https://stackoverflow.com/questions/39315219/custom-marker-with-user-image-inside-the-pin
