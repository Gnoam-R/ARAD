## 🔍 FCM (Firebase Cloud Messaging)

FCM은 Firebase Cloud Messaging의 약자로, 무료로 메시지를 안정적으로 전송할 수 있는 교차 플랫폼 메시징 솔루션입니다.
제가 FCM을 사용하는 목적은 저의 회사 서버 측에서 앱의 사용자에게 하여금 특정 상황에 대한 이벤트 메시지를 전달하기 위해서 입니다.
가령 사용자가 사진 촬영을 통해 서버에 이미지, 사용자 ID, 위치 정보 등을 전달하게 될 때 나타날 수 있는 상황들에 대한 메시지 또는 포인트 지급을 위해 사용자에게 이벤트 상황을 공유하기 위해서 사용되곤 합니다.

위와 같은 이유로 저는 Firebase 프랫폼을 사용하여 FCM message를 전달 받을 수 있었는데 오픈된 레퍼런스들이 많이 존재하여 쉽게 구현할 수 있었습니다.

```kotlin
class MyFirebaseMessagingService:FirebaseMessagingService() {
    ...
    /** 알림 생성 메서드 */
    private fun sendNotification(remoteMessage: RemoteMessage) {
        // RequestCode, Id를 고유값으로 지정하여 알림이 개별 표시
        val uniId: Int = (System.currentTimeMillis() / 7).toInt()

        // 일회용 PendingIntent : Intent 의 실행 권한을 외부의 어플리케이션에게 위임
        val intent = Intent(this, com.example.arad_january.views.main.advertise.CoupangPartnerActivity::class.java)
        intent.flags = Intent.FLAG_ACTIVITY_CLEAR_TOP or Intent.FLAG_ACTIVITY_SINGLE_TOP
        val pendingIntent = PendingIntent.getActivity(this, uniId, intent, PendingIntent.FLAG_ONE_SHOT)

            //각 key, value 추가
        for(key in remoteMessage.data.keys){
            intent.putExtra(key, remoteMessage.data.getValue(key))
        }
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP) 

        ...

        val notificationManager = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

        // 알림 생성
        notificationManager.notify(uniId, notificationBuilder.build())
    }
    ...
}

```

[FCM 레퍼런스]((https://seopseop911.tistory.com/20))를 보면 파이어베이스에 등록후 `cloud messaging` 절차를 진행하게 되면 `sendNotification` 함수가 실행되게 됩니다.

``` xml
<!--        서비스-->
        <service android:name=".service.MyFirebaseMessagingService"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT"/>
            </intent-filter>

        </service>
```

exported = true로 설정해야지 외부(파이어베이스)에서 `com.google.firebase.MESSAGING_EVENT` Action을 실행하여 `MyFirebaseMessagingService` 클래스에 접근할 수 있습니다.

```kotlin
    // 
    private fun sendNotification(remoteMessage: RemoteMessage) {
        ...
        val channelId = "my_channel"
        val soundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION)    // 알림 소리

        // 알림에 대한 UI 정보, 작업
        val notificationBuilder = NotificationCompat.Builder(this, channelId)
            .setSmallIcon(R.drawable.winter1) // 아이콘 설정
            .setContentTitle(remoteMessage.data["title"].toString()) // 제목
            .setContentText(remoteMessage.data["body"].toString()) // 메시지 내용
            .setAutoCancel(true) // 알람클릭시 삭제여부
            .setSound(soundUri)  // 알림 소리
            .setContentIntent(pendingIntent) // 알림 실행 시 Intent
        ...
    }
```

## 📝 Feature Check List
  - [x] 파이어베이스 cloud messaging
  - [x] 푸시 알림 구현
  - [x] 알림 클릭시 화면 전환


## 📷 Screenshot

<!-- 작업한 화면이 있다면 스크린 샷으로 첨부해주세요. -->

<h1 align="center">

|    푸시 알람    |   푸시 알람 클릭   |
| :-------------: | :-------------: |
| <img src="https://github.com/user-attachments/assets/50661241-3af5-4fee-838a-7d72bfc5c856" width="500" height="150"/> | <img src="https://github.com/user-attachments/assets/5a4d97a5-99f6-48d6-a983-7bf1e511e332" width="200" height="450"/> | 


</h1>

## 📮 관련 이슈

