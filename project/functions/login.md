## 🔍 Login
소셜 로그인은 구글과 카카오에서 제공하는 API를 이용하여 구현했습니다.
사용자는 OAuth를 통해 Access Token을 받아 인증 토큰으로 소셜 플랫폼에 접속합니다.
이후, 사용자의 접근 가능한 로그인 정보를 자체 서버에 전달 합니다.

## 📝 Feature Check List
  - [x] 소셜 로그인(카카오, 구글)
  - [x] 회원 가입, 탈퇴 기능 구현
  - [x] 로그인 토큰 수령 및 유저 정보 수집
  - [x] UX 고도화

## 📷 Screenshot

<!-- 작업한 화면이 있다면 스크린 샷으로 첨부해주세요. -->

<h1 align="center">

|    소셜 로그인1    |   소셜 로그인2   |  소셜 로그인3 |
| :-------------: | :-------------: | :-------------: |
| <img src="https://github.com/user-attachments/assets/4e724765-1df1-4c0b-b905-6f19a8fc40e8" width="200" height="450"/> | <img src="https://github.com/user-attachments/assets/8554ba75-df1b-4ec6-9f04-e1d000466dcf" width="200" height="450"/> | <img src="https://github.com/user-attachments/assets/3dbf7731-770c-4975-8a30-223b925d0ee6" width="200" height="450"/> |


</h1>

## 📮 관련 이슈

### 로그인 기능 클래스화 
로그인 기능을 해당 화면에서만 사용하는 것이 아닌 앱 자체에서 소셜 로그인 & 사용자 정보 기능을 요청

객체 지향 프로그래밍의 개념이 없던 상태로 프로젝트를 진행하게 되었고, 로그인 기능을 따로 분리하여 호출하는 방식으로 Repository 디자인 패턴을 활용하여 구현하는 방식으로 코드를 수정하게 되었습니다.

Repository 패턴 적용 후 로그인 화면에서 호출 예시
```kotlin
class LoginActivity : BaseActivity<ActivityLoginMainBinding>({ActivityLoginMainBinding.inflate(it)}) {
    private val KakaoRepository: KakaoLoginRepository = KakaoLoginRepositoryImpl()
    private val GoogleRepository: GoogleLoginRepository = GoogleLoginRepositoryImpl()
    ...

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setLogin()              // 구글, 카카오 소셜 로그인 섵정

        binding.btnStartKakaoLogin.setOnClickListener{
            KakaoRepository.SignIn()
        }
        binding.btnStartGoogleLogin.setOnClickListener {
            GoogleRepository.GoogleSignIn(resultLauncher)
        }
    }

    private fun setLogin() {
        KakaoRepository.setLogin(this, context = this)
        GoogleRepository.setLogin(this, context = this)
    }
    ...
```
