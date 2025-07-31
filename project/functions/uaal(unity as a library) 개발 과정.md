# Android에서 UAAL(Unity as a Library) 활용 가이드

## 목차
1. [UAAL 개요](#uaal-개요)
2. [개발 환경 설정](#개발-환경-설정)
3. [Unity 프로젝트 설정](#unity-프로젝트-설정)
4. [Android 프로젝트 통합](#android-프로젝트-통합)
5. [Gradle 설정](#gradle-설정)
6. [매니페스트 설정](#매니페스트-설정)
7. [코드 구현](#코드-구현)
8. [문제 해결](#문제-해결)
9. [참고 자료](#참고-자료)

---

## UAAL 개요

UAAL(Unity as a Library)은 Unity 프로젝트를 Android 네이티브 애플리케이션에 라이브러리 형태로 통합하는 기술입니다. 이를 통해 기존 Android 앱에서 Unity로 개발된 게임이나 3D 콘텐츠를 실행할 수 있습니다.

### 주요 특징
- Android 네이티브 앱에서 Unity 콘텐츠 실행
- 버튼 클릭 등의 이벤트를 통한 Unity 화면 전환
- 두 플랫폼 간의 데이터 교환 가능
- 독립적인 프로세스 실행으로 안정성 확보

### 활용 사례
- 게임 기능이 포함된 일반 앱
- AR/VR 기능 통합
- 3D 시각화 도구
- 교육용 인터랙티브 콘텐츠

---

## 개발 환경 설정

### 필수 요구사항
1. **Unity 설치**
   - Unity 공식 웹사이트(https://unity.com/kr)에서 다운로드
   - Personal 플랜 선택 후 설치

2. **Android NDK 설치**
   - IL2CPP 빌드에 필요
   - Android Developer 사이트에서 다운로드
   - NDK 경로 설정 필요

3. **필요 리소스**
   - Unity-Technologies/uaal-ar-example GitHub 프로젝트
   - 안드로이드 기본 프로젝트
   - 유니티 프로젝트

---

## Unity 프로젝트 설정

### API 레벨 설정
Unity에서 Android 라이브러리로 내보낼 때 API 호환성 문제가 발생할 수 있습니다.

```
BuildSetting -> Player Setting -> Other Settings -> Minimum API Level
```
- **문제**: API 레벨 19로 설정되어 있을 때 호환성 오류
- **해결**: Minimum API Level을 19에서 24로 업데이트

### Unity 프로젝트 내보내기
1. File → Build Settings
2. Platform을 Android로 설정
3. Export Project 체크
4. 적절한 경로에 프로젝트 내보내기

---

## Android 프로젝트 통합

### UnityLibrary 파일 이동
내보낸 Unity 프로젝트의 UnityLibrary 폴더를 Android 프로젝트 루트에 복사합니다.

### 프로젝트 구조
```
AndroidProject/
├── app/
├── unityLibrary/
│   ├── libs/
│   ├── src/
│   └── build.gradle
└── settings.gradle.kts
```

---

## Gradle 설정

### settings.gradle.kts 수정
```kotlin
include(":unityLibrary")
project(":unityLibrary").projectDir = file("./unityLibrary")
```

### app/build.gradle.kts 수정
```kotlin
dependencies {
    implementation(project(":unityLibrary"))
    implementation("com.google.guava:guava:31.0.1-jre")
}
```

### Android Gradle Plugin 버전 조정
호환성 문제 발생 시:
- Android Gradle Plugin을 8.1.0에서 7.3.1로 다운그레이드
- UnityStreamingAssets 설정 추가

### NDK 경로 설정
```kotlin
android {
    ndkPath = "path/to/your/ndk"
}
```

### 중복 AAR 파일 처리
```kotlin
// unityLibrary/libs 폴더에서 중복 AAR 파일 제거
// build.gradle에서 해당 종속성 제거
```

---

## 매니페스트 설정

### UnityLibrary 매니페스트 수정

#### Intent Filter 제거
중복 앱 패키지 방지를 위해 다음 블록을 제거합니다:

```xml
<!-- 제거할 부분 -->
<intent-filter>
    <category android:name="android.intent.category.LAUNCHER" />
    <action android:name="android.intent.action.MAIN" />
</intent-filter>
```

#### Unity 프로세스 설정
```xml
<activity
    android:name="com.unity3d.player.UnityPlayerActivity"
    android:process=":Unity"
    android:exported="true">
</activity>
```

**목적**: Unity 앱이 독립적으로 종료될 수 있도록 하여 전체 Android 프로세스 종료를 방지

### strings.xml 리소스 추가
```xml
<resources>
    <string name="game_view_content_description">Game View</string>
</resources>
```

---

## 코드 구현

### MainActivity 구현 (Kotlin)
```kotlin
class MainActivity : ComponentActivity() {
    lateinit var unityIntent: Intent
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Unity 앱 인텐트 생성
        unityIntent = Intent(this, UnityPlayerActivity::class.java)
        
        val button = findViewById<Button>(R.id.button_start)
        button.setOnClickListener {
            startActivity(unityIntent)
        }
    }
}
```

### Unity와의 메시지 통신
UnityPlayer 클래스를 사용하여 Unity 게임 오브젝트에 메시지를 전송할 수 있습니다:

```kotlin
// Unity 게임 오브젝트에 메시지 전송
// 필요한 매개변수:
// - 타겟 게임 오브젝트
// - 스크립트 메서드 이름
// - 파라미터 (문자열)
UnityPlayer.UnitySendMessage("GameObjectName", "MethodName", "parameter")
```

---

## 문제 해결

### 일반적인 오류와 해결책

#### 1. API 레벨 호환성 문제
**증상**: Unity 프로젝트 내보내기 시 API 호환성 오류
**해결**: Minimum API Level을 19에서 24로 업데이트

#### 2. Gradle 버전 호환성 문제
**증상**: 네임스페이스 및 버전 호환성 이슈
**해결**: Android Gradle Plugin을 7.3.1로 다운그레이드

#### 3. 중복 클래스 오류
**증상**: 빌드 시 중복 클래스 에러
**해결**: unityLibrary/libs 폴더에서 중복 AAR 파일 제거

#### 4. NDK 경로 설정 문제
**증상**: IL2CPP 빌드 실패
**해결**: 
- Android NDK 다운로드 및 설치
- build.gradle에서 NDK 경로 정확히 설정

#### 5. 라이브러리 종속성 문제
**증상**: 빌드 또는 런타임 오류
**해결**: 
- Guava 라이브러리 추가
- 중복 종속성 제거
- 라이브러리 호환성 확인

### 빌드 오류 해결 단계
1. Clean Project 실행
2. Rebuild Project 실행
3. Gradle 캐시 정리
4. NDK 경로 재확인
5. 종속성 충돌 해결

---

## 참고 자료

### 공식 리소스
- [Unity 공식 웹사이트](https://unity.com/kr)
- [Unity Technologies UAAL Example](https://github.com/Unity-Technologies/uaal-ar-example)
- [Android NDK 다운로드](https://developer.android.com/ndk/downloads)

### 관련 프로젝트
- [GitHub: rohhyungwoo/ARAD_Public](https://github.com/rohhyungwoo/ARAD_Public)
- [Google Play: ARAD](https://play.google.com/store/apps/details?id=com.anyractive.arad_january)

### 추가 학습 자료
- [Learn and Give 블로그](https://learn-and-give.tistory.com/86)
- Unity 공식 문서
- Android Developer 가이드

---

## 결론

UAAL을 활용한 Unity와 Android의 통합은 복잡한 설정 과정을 거쳐야 하지만, 성공적으로 구현하면 강력한 하이브리드 애플리케이션을 개발할 수 있습니다. 

### 핵심 성공 요소
1. **정확한 API 레벨 설정**: Unity와 Android 프로젝트 간의 호환성 확보
2. **세심한 Gradle 설정**: 종속성 관리 및 버전 호환성 해결
3. **적절한 매니페스트 설정**: 중복 앱 방지 및 프로세스 분리
4. **NDK 환경 구성**: IL2CPP 빌드를 위한 필수 설정

이 가이드를 따라 단계별로 진행하면서 각 단계에서 발생할 수 있는 문제들을 해결해 나가면, 안정적인 UAAL 통합을 달성할 수 있습니다.
