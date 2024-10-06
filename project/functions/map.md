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

### 구글 맵 & 마커 커스텀

- 구글 맵 커스텀
```kotlin
//GoogleMapRepositoryImpl.kt

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
      
- 마커 커스텀
      - 

### POI 위 경도 데이터 기반 카메라 이동 및 마킹
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
