- OAuth 2.0
  https://engineerinsight.tistory.com/163#google_vignette
  https://ksh-coding.tistory.com/62
  ***
  ## OAuth 2.0이란
  OAuth(”Open Authorization”)는 인터넷 사용자들이 **비밀번호를 제공하지 않고** 다른 웹사이트 상의 자신들의 정보에 대해 웹사이트나 애플리케이션의 **접근 권한을 부여할 수 있는 공통적인 수단으로 사용되는, 접근 위임을 위한 개방형 표준**이다.
  - 사용자가 자신의 계정 정보를 공유하지 않고, 다른 서비스에 대한 접근 권한을 안전하게 제공하는 서비스
  - OAuth를 지원하는 서비스는 사용자로부터 인증을 받은 후, 해당 서비스에 대한 접근 권한을 받는다.
    > **‘인증은 유저가 직접, 권한은 서비스에게’**
  ### 탄생 이유
  | 주체      | 문제                    |
  | --------- | ----------------------- |
  | 사용자    | ID/PW를 못 믿고 넘김    |
  | 서비스(A) | 비밀번호 유출 책임 100% |
  | 네이버    | 외부 서비스 신뢰 불가   |
  - **ID/PW는 인증 서버만 알게 하고**
  - 서비스는 토큰만 받게 만들자 → OAuth 탄생
  ***
  ### OAuth 내 역할
  ![image.png](attachment:1a140bc2-c0be-437e-9524-cfb8ebf37e8d:image.png)
  **1. Resource Owner (자원 소유자) == 실제 사용자**
  > 자기 리소스를 가진 사람 = 사용자
  - 네이버, 구글, 카카오 계정을 실제로 가진 사람
  - 아이디/비밀번호 입력 주체
  - "이 서비스에게 접근 권한 줄래?" 결정 주체
    **2. Client (클라이언트) == 내 서비스**
    > 정보가 필요한 나의 서비스 서버
  - 사용자 정보가 필요한 서비스
  - 직접 로그인하지 않음
  - Authorization Server에 인증 위임
  - 토큰 받아서 Resource Server 호출
    **3. Authorization Server == 네이버 소셜 로그인 인증 서버**
    > 로그인 담당 서버
  - 사용자 인증 담당
  - scope 동의 관리
  - 토큰 발급
    **4. Resource Server == 네이버에서 사용자 리소스를 관리/제공하는 서버**
    > 사용자 데이터가 있는 서버
  - 프로필 제공
  - 이메일 제공
  - 캘린더 제공
  - 친구 목록 제공
    > **Authorization Server ≠ Resource Server
    > 둘은 같을 수도 있고 다를 수도 있음**
    > Authorization Server는 토큰 발급자
    >
    > Resource Server는 **API 제공자**
  ***
  ### 흐름
  ![image.png](attachment:07e0addb-972d-45c7-8184-70d6b51f7c58:image.png)
  ![image.png](attachment:55e7026a-f806-47dc-b0e6-f4f718dfb06a:image.png)
  - **사용자 - Resource Owner**
  - **서비스(사용자가 이용하려는 서비스) - Client**
  - **PAYCO 인증 서비스 - Authorization Server**
  - **PAYCO API 서비스 - Resource Server**
  ***
  ### ETC.
  ### **OAuth 는 로그인에 한정된 것이 아니다.**
  OAuth 프로토콜은 로그인을 하기 위해 만들어진 것이 아니다. 이름부터 OpenAuthorization (인가) 이기 때문에 로그인 뿐만 아니라 Client 가 Resource Server 에서 사용할 수 있는 권한이 포함되어 있다. 로그인 접근 권한도 그 권한 중에 하나인 것이다. 필요에 따라 Resource Owner 가 허용하면 Resource Server 로부터 데이터파일 등 다른 권한도 부여받을 수 있다.
  (어디에 쓸수있는지는 더 알아볼것)
  ### **OAuth 1.0 vs. OAuth 2.0**
  Auth 2.0 은 OAuth 1.0 에서 보안적인 부분이 개선되었다.
  - 만료 기한이 없었던 Access Token 은 만료 기한이 생겼다. Client 가 관리하는 Access Token 이 만료되면 다시 사용자에게 권한을 부여받아야 하는데, 매번 만료될 때마다 물어보는 것은 불필요하다. 따라서 Refresh Token 개념이 나오게 되었고, Access Token 이 만료되면 Refresh Token 으로 Resource Server 로부터 새로운 Access Token 을 발급받을 수 있다.
  ***
- JWT
  https://ivory-room.tistory.com/88

  ***

  ### **JWT란?**

  > JWT는 authentication 상태를 저장하지 않고
  >
  > 사용자 신원을 증명하는 stateless 인증 토큰이다.
  > JWT = 위조 불가능한 인증 토큰
  > **JWT = 서명(Signature) 기반 토큰이다.**
  > | 특징 | 설명 |
  > | -------------- | ---------------------------------- |
  > | Stateless | 서버가 로그인 상태를 저장하지 않음 |
  > | Self-contained | 토큰 안에 인증 정보 포함 |
  > | Tamper-proof | 위변조 여부 검증 가능 |
  > | Portable | 어디든 전송 가능 (Header / Cookie) |
  > ![image.png](attachment:5ab9c181-ff90-4161-b356-603acf9dc69f:image.png) > **JWT는 "암호화된 데이터"가 아니다.**
  > JWT의 payload는:
  > 단순한 Base64 인코딩일 뿐
  >
  > → 누구나 디코딩 가능

  ### 그럼 JWT 보안은 어디서 나오냐?

  JWT의 보안은 서명(Signature)에서 나온다.
  **서명이 하는 일:**
  | 기능 | 설명 |
  | ----------- | ------------------------ |
  | 위조 방지 | payload 조작 불가 |
  | 무결성 검증 | 데이터가 바뀌었는지 확인 |
  | 발급자 인증 | 서버가 발급했는지 증명 |

  ### but…

  **서명은 내용을 숨기지 않는다.**

  ### 그래서…

  JWT가 보장하는 것

  - 변조 감지
  - 발급자 확인
    JWT가 보장 안 하는 것
  - 내용 비공개

  - 기밀성

  - 데이터 숨김

  ### 실제 운영 환경에서는 HTTPS가 암호화 담당

  > TLS가 네트워크 암호화
  >
  > JWT는 무결성 담당
  > ⇒ 만약 JWT 자체를 암호화 해야된다면 JWE 사용

  ### 그래서 결론적으로

  > JWT는 기본적으로 암호화가 아니라 서명 기반 무결성 토큰이다.
  >
  > Payload는 누구나 디코딩 가능하고, 위변조만 방지한다.
  >
  > 내용 보호가 필요할 경우 JWE를 사용한다.

  ***

  ### ETC.

  > 해시 ≠ 암호화
  >
  > 해시 ≠ 서명
  >
  > 서명은 "해시 + 비밀키"
  > **같은거라 생각했는데, 서로 살짝 다른 느낌.**
  > 암호화는 데이터 내용을 보호하는 기술이며,
  >
  > 해시와 서명은 무결성과 위변조 방지를 위한 기술이다.
  > ⇒ 해쉬 + 비밀키는 **확인용**이지, **보기용**이 아니다.
  > | 개념 | 목적 | 되돌림 | 숨김 |
  > | ------ | ----------- | ------ | ---- |
  > | 해시 | 무결성/지문 | 불가 | X |
  > | 암호화 | 기밀성 | 가능 | O |
  > | 서명 | 위조 방지 | 불가 | X |

  ***

- Bearer Token
  https://apidog.com/kr/blog/jwt-vs-bearer-token-2/
  ***
  > JWT는 무엇을 담았느냐,
  >
  > Bearer는 **어떻게 보내느냐**의 차이다.
  ### Bearer 토큰이란?
  Bearer Token은 **토큰 전달 방식**이다.
  ```
  Authorization: Bearer <Token>
  ```
  이 말의 의미는:
  > 이 토큰을 가진 자를 사용자로 인정한다.
  ***
  ### Bearer Token의 특징
  - 토큰을 들고 있으면 권한 인정 (**Bearer Token은 소유 기반 인증이다)**
  - 토큰 안 구조는 중요하지 않음
  - JWT도 Bearer 토큰으로 전달 가능
  - OAuth Access Token도 Bearer 방식 사용
  ### 장단점
  장점:
  - 구현 단순
  - 다양한 토큰 형식과 호환
  - API 인증 표준 방식
    단점:
  - 유출 시 바로 계정 탈취 가능
  - 서버 측 제어 없으면 폐기 어려움
  - HTTPS 없으면 매우 위험 (자체 암호화 X)
  ***
  Bearer Token은 소유한 사람이 주인이라고 생각하는 방식.
  > 즉, 훔친 사람도 주인이다.
  ### 그래서 필수
  - HTTPS
  - 만료 시간(exp)
  - Refresh Token
  ## 보안 핵심 원칙
  Bearer든 JWT든 **보안을 지키는 것은 HTTPS**이다.
  토큰 자체가 안전한 것이 아니다.
  전달 경로가 안전해야 한다.
  ***
  ### 결론
  > Bearer Token은 Authorization 헤더에 포함되는 토큰 전달 정책이며, 토큰 소유 여부만으로 인증을 수행하는 방식이다.
  > 기존에는 비밀번호를 직접 전달하는 인증 방식이라 위험했지만, Bearer Token 방식은 만료 가능한 권한만 전달하므로 안전!
  ***
