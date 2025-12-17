- CI/CD
  https://ccomccomhan.tistory.com/297
  ***
  # CI/CD
  ## 1. 기존 개발·배포 방식의 문제점
  전통적인 개발 환경에서는 다음과 같은 흐름이 일반적이었다.
  - 개발자가 각자 코드를 작성
  - 일정 시점에 수동으로 코드 병합
  - 사람이 직접 빌드, 테스트, 배포 수행
    이 방식의 문제점은 다음과 같다.
  - 코드 병합 시 충돌 발생 가능성 큼
  - 테스트 누락으로 인한 오류가 운영 환경에서 발견됨
  - 배포 과정이 복잡하고 사람 실수에 의존
  - 배포가 야간이나 특정 인력에게 집중됨
  - 장애 발생 시 원인 추적과 복구가 느림
    이러한 문제로 인해 개발 속도는 느려지고, 배포는 위험한 작업이 되었다.
  ***
  ## 2. CI/CD의 목적
  CI/CD는 위와 같은 문제를 해결하기 위해 등장한 **개발·배포 자동화 방식**이다.
  핵심 목적은 다음과 같다.
  - 코드 통합 과정에서의 오류를 조기에 발견
  - 반복적인 수작업 제거
  - 배포 속도 향상
  - 운영 안정성 확보
  ***
  # CI (Continuous Integration, 지속적 통합)
  ### 정의
  CI는 개발자가 작성한 코드를 **자주 저장소에 병합하고**,
  그때마다 **자동으로 빌드와 테스트를 수행하는 과정**이다.
  ### CI의 핵심 개념
  1. **빈번한 코드 병합**
     - 작은 단위로 개발
     - 자주 merge하여 충돌 최소화
  2. **통합 과정의 자동화**
     - 코드 변경 시 자동으로 빌드
     - 자동 테스트(Unit Test, Integration Test 등) 수행
     - 실패 시 병합 차단
     -
  ### CI 흐름
  1. 개발자가 코드 수정
  2. Git 저장소에 push 또는 PR 생성
  3. CI 파이프라인 실행
     - 빌드
     - 테스트
  4. 성공 시 병합 가능, 실패 시 병합 불가
  ### CI의 효과
  - 코드 충돌 조기 발견
  - 오류의 원인을 빠르게 특정 가능
  - 코드 품질 유지
  - 통합 과정에 대한 신뢰성 확보
  ***
  ***
  ***
  # CD (Continuous Delivery / Continuous Deployment)
  CD는 CI를 통과한 코드를 **배포 단계까지 자동으로 연결하는 개념**이다.
  CD는 두 가지로 구분된다.
  ### Continuous Delivery (지속적 전달)
  - 빌드, 테스트, 스테이징 배포까지 자동화
  - 운영 서버 배포는 **수동 승인**
    특징:
  - 안정성 중시
  - QA 또는 검증 단계 필요
  - 배포 자체는 버튼 한 번으로 가능
  ### 4-2. Continuous Deployment (지속적 배포)
  - 테스트를 통과한 코드가 **자동으로 운영 서버에 배포**
  - 사람의 개입 없음
    특징:
  - 배포 속도 중시
  - 잦은 기능 개선에 적합
  - 자동 롤백, 모니터링 체계 중요
  ***
  ## 5. CI와 CD의 차이
  - CI: 코드 **통합과 검증**에 초점
  - CD: 검증된 코드를 **배포 또는 배포 직전까지 전달**하는 단계
    CI는 CD의 전제 조건이며,
    CI → CD로 이어지는 전체 흐름을 **CI/CD 파이프라인**이라고 한다.
  ***
  ## 6. CI/CD 파이프라인 전체 흐름
  1. 코드 작성
  2. Git 저장소 반영
  3. CI
     - 자동 빌드
     - 자동 테스트
  4. CD
     - 스테이징 배포
     - 운영 배포(수동 또는 자동)
  5. 배포 후 모니터링
  6. 문제 발생 시 자동 감지 및 롤백 가능
     모든 과정은 미리 정의된 규칙에 따라 자동으로 수행되며, 로그로 기록된다.
  ***
  ## 7. CI/CD가 필요한 이유 정리
  - 반복 작업 자동화 → 실수 감소
  - 배포 주기 단축
  - 장애 대응 속도 향상
  - 개발자는 기능 개발에 집중 가능
  - 배포가 위험한 이벤트가 아닌 일상적인 작업이 됨
  ***
- GitHub Actions
  https://velog.io/@ggong/Github-Action%EC%97%90-%EB%8C%80%ED%95%9C-%EC%86%8C%EA%B0%9C%EC%99%80-%EC%82%AC%EC%9A%A9%EB%B2%95

  ***

  ## GitHub Actions란

  - GitHub에서 공식 제공하는 **CI/CD 자동화 도구**
  - GitHub Repository에서 발생하는 이벤트를 기준으로
    - 빌드
    - 테스트
    - 배포
    - 기타 반복 작업
      을 **자동으로 실행**할 수 있게 해줌
  - 설정은 **YAML 파일**로 작성
    설정 파일 위치:

  ```yaml
  .github/workflows/*.yaml
  ```

  ***

  ### **GitHub Actions 전체 구조 개요**

  ```yaml
  Event
  ↓
  Workflow
  ↓
  Job
  ↓
  Step
  ↓
  Action / Command
  ```

  ### Work Flow 전체 설정 예시

  ```yaml
  # ================================
  # Workflow 전체 설정
  # ================================

  # GitHub Actions 탭에 표시될 workflow 이름
  name: example-github-actions-workflow

  # ================================
  # Event (Workflow 트리거 조건)
  # ================================
  # 어떤 이벤트가 발생했을 때 이 workflow를 실행할지 정의
  on:
    # main 브랜치에 push가 발생하면 실행
    push:
      branches: [ main ]

    # main 브랜치로 pull request가 생성되면 실행
    pull_request:
      branches: [ main ]

  # ================================
  # Job 정의
  # ================================
  jobs:
    build-and-test:
      # 이 job이 실행될 Runner(OS)
      runs-on: ubuntu-latest

      # ================================
      # Step 정의 (순차 실행)
      # ================================
      steps:
        # Step 1: 레포지토리 코드 체크아웃
        # GitHub Actions에서 제공하는 공식 Action
        - name: Checkout repository
          uses: actions/checkout@v4

        # Step 2: Node.js 환경 설정
        # Marketplace Action 사용
        - name: Setup Node.js
          uses: actions/setup-node@v4
          with:
            node-version: 18

        # Step 3: 의존성 설치
        # run은 Runner의 shell에서 명령 실행
        - name: Install dependencies
          run: npm install

        # Step 4: 테스트 실행
        - name: Run tests
          run: npm test

        # Step 5: 빌드 실행
        - name: Build project
          run: npm run build

        # Step 6: 실행 확인용 로그 출력
        - name: Print job status
          run: echo "Job finished with status: ${{ job.status }}"
  ```

  ## workflow 구조 정리

  ### 1. Workflow

  ```yaml
  name:
  on:
  jobs:
  ```

  - YAML 파일 하나 = Workflow 하나
  - 자동화 전체 흐름 정의

  ***

  ### 2. Event

  ```yaml
  on:
  push:
  pull_request:
  ```

  - Workflow를 실행시키는 트리거
  - push, PR, schedule 등 가능

  ***

  ### 3. Job

  ```yaml
  jobs:
  build-and-test:
  runs-on:
  ```

  - Workflow 안의 실행 단위
  - 하나의 Runner에서 실행됨
  - 여러 Job 만들면 병렬 실행 가능

  ***

  ### 4. Runner

  ```yaml
  runs-on:ubuntu-latest
  ```

  - Job이 실제로 실행되는 가상 머신
  - GitHub에서 제공

  ***

  ### 5. Step

  ```yaml
  steps:
  -name:
  run/uses
  ```

  - Job 내부에서 순서대로 실행
  - 실패하면 Job 전체 실패

  ***

  ### 6. Action

  ```yaml
  uses:actions/checkout@v4
  ```

  - Step에서 사용하는 재사용 가능한 작업
  - Marketplace 또는 커스텀 Action 가능

  ***

  ### 7. run vs uses 차이

  - `run`
    → 쉘 명령 직접 실행
  - `uses`
    → 이미 만들어진 Action 실행

  ***

  ### ETC) **Managed CI/CD**

  - **내부적으로는 전부 CI/CD 도구**를 쓰되
  - **사용자가 설정 파일을 거의 안 만지게** 추상화해둔 것임
    | 항목 | GitHub Actions | Vercel / Cloud Build |
    | --------- | -------------------- | -------------------- |
    | 설정 | 내가 YAML 직접 작성 | 대부분 자동 |
    | 유연성 | 최강 | 제한적 |
    | 러너 | GitHub / Self-hosted | 플랫폼 전용 |
    | 배포 대상 | 아무 데나 | 자기 플랫폼 최적화 |
    | 난이도 | 중~상 | 하 |

- Reverse Proxy
  https://velog.io/@jmjmjmz732002/Infra-Reverse-Proxy..-%EA%B3%BC%EC%97%B0-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C%EC%9A%94
  ***
  # Reverse Proxy & Nginx
  ***
  ## Proxy 개념
  ### Proxy란
  - ‘대리’라는 의미
  - **클라이언트와 서버 사이에서 요청과 응답을 중계하는 서버**
  - 네트워크 상에서 **중간 게이트 역할**
    프록시 서버의 기본 역할:
  - 요청 중계
  - 응답 캐싱
  - 보안 및 접근 제어
  - 트래픽 제어
  ***
  ***
  ## Proxy 서버의 종류
  프록시 서버는 **위치 기준**으로 두 가지로 나뉜다.
  ### Forward Proxy
  ![image.png](attachment:a093110a-eee3-4c86-8612-c83fb1095062:image.png)
  ### 위치
  - **클라이언트 앞단**
  ### 동작 방식
  1. 클라이언트 → Forward Proxy
  2. Forward Proxy → 실제 서버
  3. 서버 → Forward Proxy → 클라이언트
  ### 특징
  - 서버 입장에서 **클라이언트가 보이지 않음**
  - 서버가 인식하는 IP는 프록시 서버의 IP
  ### 사용 목적
  - 클라이언트 IP 은닉
  - 내부 네트워크 보안
  - 접근 제어 및 필터링
  - 캐싱을 통한 응답 속도 개선
  ### 대표 사용 사례
  - 회사·학교 내부망 인터넷 접근 제한
  - 방화벽, 콘텐츠 필터링
  ***
  ### Reverse Proxy
  ![image.png](attachment:1575d326-c97f-4d63-b955-f5e208a5251e:image.png)
  ### 위치
  - **서버 앞단**
  ### 동작 방식
  1. 클라이언트 → Reverse Proxy
  2. Reverse Proxy → 내부 서버(Web/WAS)
  3. 내부 서버 → Reverse Proxy → 클라이언트
  ### 특징
  - 클라이언트 입장에서 **실제 서버가 보이지 않음**
  - 서버의 IP와 구조를 은닉
  ### 사용 목적
  - 서버 보안 강화
  - 트래픽 분산
  - 무중단 배포
  - SSL 처리
  - 캐싱
  ***
  ## Reverse Proxy가 필요한 이유
  ### 보안
  - 실제 서비스 서버 IP 은닉
  - DDoS, 직접 공격 방어
  - 요청 필터링 및 차단 가능
  ### 서버 구조 분리
  - Web Server와 WAS 분리
  - 일반적인 구조:
    ```
    Client → Nginx → WAS → DB
    ```
  ### 트래픽 분산
  - Load Balancing과 결합 가능
  - 여러 서버로 요청 분산 처리
  ### 확장성
  - 서버 추가/제거 용이
  - 무중단 서비스 확장 가능
  ### 캐싱
  - 자주 요청되는 리소스 캐싱
  - 백엔드 서버 부하 감소
  ### SSL 처리
  - Reverse Proxy에서 SSL 종료
  - 내부 서버의 SSL 부담 감소
  ***
  ## Reverse Proxy 활용 예
  ### 점검 페이지 분기
  - 외부 IP → 점검 페이지
  - 내부 IP → 정상 서비스
  ### 무중단 배포
  - 신규 서버 기동
  - 트래픽 전환
  - 기존 서버 종료
  ### Web / API 분리
  - 정적 리소스와 API 서버 분리 처리
  ***
- HTTPS
  https://yoon4360.tistory.com/50
  ***
  # HTTPS (Feat. Nginx)
  ***
  ## HTTPS란?
  **HTTPS (Hypertext Transfer Protocol Secure)**
  → HTTP에 **SSL/TLS 암호화가 추가된 통신 프로토콜**
  → 클라이언트(브라우저)와 서버 간 데이터를 **암호화해서 안전하게 주고받는 구조**
  HTTPS의 핵심 목적
  - **데이터 기밀성 보장**
    → 전송 중 데이터가 노출되거나 도청되는 것 방지
  - **데이터 무결성 보장**
    → 중간에서 내용이 변조되는 것을 방지
  - **서버(사이트) 인증**
    → 브라우저가 신뢰할 수 있는 CA(Certificate Authority)가 발급한 인증서를 이용해 서버가 진짜임을 검증 ([위키백과](https://en.wikipedia.org/wiki/HTTPS?utm_source=chatgpt.com))
    HTTPS는 TCP 상에서 **기본 포트 443번**을 사용하며,
    HTTP처럼 텍스트 자체를 전송하는 것이 아니라
    **TLS/SSL 계층 위에 HTTP를 얹어 암호화 통신을 함**
    SSL/TLS의 기본 과정:
  1. 클라이언트가 HTTPS 접속 시도
  2. 서버가 **인증서(Cert)** 전송
  3. 클라이언트가 인증서를 검증
  4. 클라이언트 ↔ 서버 간 **세션 키 교환**
  5. 이후의 통신은 모두 **암호화된 채널로 교환됨**
     이 구조 덕분에 비밀번호나 민감정보 같은 데이터가 네트워크 상에서 쉽게 노출되지 않게 된다.
  ***
  ## SSL 인증서의 구조와 역할
  SSL/TLS 인증서는 다음 두 구성 요소로 이루어진다
  - **Private Key (비밀키)**
    → 서버만 알고 있는 키
    → 암호화된 데이터의 복호화에 사용됨
  - **Certificate (공개키 + 서명)**
    → 클라이언트에게 전달되는 공개정보
    → 도메인과 CA 서명이 포함됨
    클라이언트는 이 공개키를 기반으로 암호화된 세션 연결을 수립하게 된다.
    인증서를 발급받기 위해서는
  - 공인 인증기관(CA)이 발급하는 SSL 인증서
  - 무료 인증서 예: Let’s Encrypt 사용 가능
  ***
  ## Nginx란?
  **Nginx**는 고성능 웹 서버 + 리버스 프록시 서버이다.
  핵심 기능
  - 정적 파일 제공 (HTML/CSS/JS)
  - 리버스 프록시(백엔드 서버 대리 중계)
  - 로드 밸런싱
  - 캐시 제공
  - HTTPS/TLS 처리
    즉 Nginx는 단순히 정적 웹을 서비스하는 서버 이상의 역할을 한다.
    특히 **Reverse Proxy + SSL 처리**는 Nginx를 많이 쓰는 주요 이유다.
  ***
  ## Nginx에서 HTTPS가 필요한 이유
  ### SSL 처리(HTTPS Offloading)
  HTTPS가 필요한 이유는 **보안 통신 보장**이지만,
  실제 애플리케이션 서버가 직접 처리하면 부담이 커질 수 있다.
  Nginx는 앞단에서 SSL/TLS 처리를 대신 수행함으로써
  - 애플리케이션 서버는 **암호화 처리 로직에서 해방**
  - 내부 서버는 HTTP 통신으로만 처리 가능
  - 공인 인증서 갱신/관리도 중앙화 가능
    즉 Nginx가 SSL 종료점(TLS termination point)가 된다.
  ***
  ## HTTPS + Reverse Proxy의 주요 역할
  ### 클라이언트와 Nginx 간 암호화
  - 브라우저 ↔ Nginx 사이가 HTTPS로 암호화
  - Nginx가 인증서로 복호화 이후 내부 서버로 전달
    → 내부 서버에는 꼭 HTTPS가 아니어도 허용 가능하지만
    필요하면 프록시→백엔드 간에도 HTTPS 연결 구성 가능
  ***
  ## HTTPS + Reverse Proxy의 장점 요약
  1. **보안 강화**
     - 암호화된 통신으로 민감 정보 보호
  2. **SSL 처리 분리**
     - 애플리케이션 서버는 암호화 로직에서 분리
  3. **단일 진입점**
     - 하나의 도메인에서 모든 서비스 통합 처리
  4. **보안 정책 중앙화**
     - 인증/접근 제어/HTTPS 설정을 일관되게 적용
  ***
  **HTTPS는 HTTP에 TLS/SSL 암호화를 더한 안전한 통신 프로토콜이며, Nginx는 HTTPS를 처리하면서 Reverse Proxy 역할로 클라이언트 요청을 내부 서버로 중계하는 구조**다.
