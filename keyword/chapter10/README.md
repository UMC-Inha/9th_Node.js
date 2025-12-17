- CI/CD
    
    ## CI/CD
    
    CI/CD: 코드 변경 사항을 자동으로 검증하고 배포하는 개발 파이프라인
    
    목적: 개발 속도 향상, 배포 안정성 확보, 사람 개입 최소화
    
    CI: 지속적 통합
    
    CD: 지속적 전달 또는 지속적 배포
    
    ## CI (Continuous Integration)
    
    정의: 여러 개발자가 작성한 코드를 자주 통합하고 자동으로 검증하는 과정
    
    핵심 목표: 항상 배포 가능한 코드 상태 유지
    
    문제 해결 대상: 통합 충돌, 테스트 누락, 빌드 실패
    
    ## CI 동작 흐름
    
    - 코드 push 또는 Pull Request
    - 소스 코드 체크아웃
    - 의존성 설치
    - 빌드 수행
    - 테스트 실행
    - 정적 분석 및 보안 검사
    - 실패 시 파이프라인 즉시 중단
    
    ---
    
    ## CI 핵심 구성 요소
    
    ### 자동 테스트
    
    Unit Test
    
    Integration Test
    
    E2E Test
    
    코드 병합 전 품질 보장 수단
    
    ### 정적 분석
    
    ESLint, Type Check, SonarQube
    
    런타임 이전 코드 품질 검사
    
    버그 및 코드 스멜 사전 검출
    
    ### 보안 검사
    
    의존성 취약점 스캔
    
    Secret(API Key, Token) 유출 탐지
    
    보안 이슈 조기 차단
    
    ## CI 트리거 방식
    
    push 이벤트
    
    pull_request 이벤트
    
    schedule 기반 정기 실행
    
    수동 실행(manual trigger)
    
    ---
    
    ## CD (Continuous Delivery / Deployment)
    
    정의: CI를 통과한 결과물을 실제 서비스 환경에 배포하는 과정
    
    역할: 검증된 코드의 안정적 서비스 반영
    
    구분 기준: 자동화 수준
    
    ---
    
    ## Continuous Delivery
    
    프로덕션 배포 직전까지 자동화
    
    최종 배포 단계에서 사람 승인 필요
    
    실무 환경에서 가장 일반적인 방식
    
    ---
    
    ## Continuous Deployment
    
    테스트 통과 후 자동 프로덕션 배포
    
    무인 배포 구조
    
    높은 테스트 신뢰도 요구
    
    ---
    
    ## CD 파이프라인 구성
    
    CI 성공 여부 확인
    
    빌드 결과물 생성
    
    서버 또는 컨테이너 배포
    
    헬스 체크
    
    문제 발생 시 롤백
    
    ---
    
    ## 배포 방식
    
    ### 전통적 배포
    
    서버 직접 파일 업로드
    
    환경 의존성 문제 빈번
    
    현재 사용 빈도 낮음
    
    ### 컨테이너 기반 배포
    
    Docker 이미지 기반 패키징
    
    환경 일관성 확보
    
    배포 및 롤백 용이
    
    현업 표준 방식
    
    ### Kubernetes 기반 배포
    
    컨테이너 오케스트레이션
    
    Deployment, Service 단위 관리
    
    Rolling Update 기본 제공
    
    대규모 서비스 적합
    
    ## 무중단 배포 전략
    
    Blue-Green 배포: 기존 버전과 신규 버전 병렬 운영
    
    Rolling Update: 인스턴스 순차 교체
    
    Canary 배포: 일부 사용자 대상 신규 버전 적용
    
    ---
    
    ## CI/CD 도구
    
    ### CI 도구
    
    GitHub Actions: GitHub 연동 중심
    
    GitLab CI: GitLab 내장
    
    Jenkins: 높은 자유도, 관리 부담
    
    ### CD 도구
    
    ArgoCD: GitOps 기반 Kubernetes 배포
    
    Helm: Kubernetes 패키지 관리
    
    ---
    
    ## 환경 분리 전략
    
    dev / staging / prod 환경 분리
    
    브랜치 또는 태그 기반 배포
    
    환경 변수 외부 관리
    
    ---
    
    ## Secrets 관리
    
    API Key, DB 비밀번호 코드 미포함
    
    CI/CD 플랫폼 Secret 저장소 사용
    
    접근 권한 최소화
    
    ---
    
    ## 롤백 전략
    
    버전 태그 기반 이미지 관리
    
    이전 버전 즉시 재배포 가능 구조
    
    Kubernetes rollout undo 활용
    
    ---
    
    ## MLOps 관점 CI/CD
    
    코드, 데이터, 모델 동시 버전 관리
    
    데이터 검증 단계 포함
    
    모델 성능 기준 기반 배포 제어
    
    모델 서빙 자동화
    
    ---
    
    ## CI/CD 구성의 이상적 조건
    
    테스트 자동화
    
    실패 시 즉시 중단
    
    배포 로그 추적 가능
    
    신속한 롤백
    
    사람 개입 최소화
    
- GitHub Action
    
    ## 기본 Workflow 예제
    
    ```yaml
    name:CIPipeline
    
    on:
    push:
    branches: [main ]
    pull_request:
    branches: [main ]
    
    ```
    
    Workflow 이름 정의
    
    Workflow 트리거 조건 정의
    
    main 브랜치에 push 또는 PR 발생 시 실행
    
    ---
    
    ## Job 정의
    
    ```yaml
    jobs:
    build:
    runs-on:ubuntu-latest
    
    ```
    
    Job 이름: build
    
    Job 실행 단위 정의
    
    runs-on: 실행 환경 지정
    
    GitHub-hosted Ubuntu Runner 사용
    
    ---
    
    ## Step 구성
    
    ```yaml
    steps:
    -name:Checkoutrepository
    uses:actions/checkout@v4
    
    ```
    
    Step 정의
    
    GitHub 저장소 코드 체크아웃
    
    actions/checkout: 공식 Action
    
    CI 파이프라인의 필수 Step
    
    ---
    
    ## 실행 환경 세팅
    
    ```yaml
    -name:SetupNode.js
    uses:actions/setup-node@v4
    with:
    node-version:18
    
    ```
    
    Node.js 실행 환경 설정
    
    버전 고정으로 환경 일관성 확보
    
    언어 런타임 세팅용 공식 Action
    
    ---
    
    ## 의존성 설치
    
    ```yaml
    -name:Installdependencies
    run:npmci
    
    ```
    
    의존성 설치 단계
    
    npm ci: lock 파일 기반 재현 가능한 설치
    
    CI 환경에서 권장 방식
    
    ---
    
    ## 테스트 실행
    
    ```yaml
    -name:Runtests
    run:npmtest
    
    ```
    
    자동 테스트 실행 단계
    
    테스트 실패 시 Job 전체 실패
    
    CI 품질 검증 핵심 단계
    
    ---
    
    ## 빌드 수행
    
    ```yaml
    -name:Buildproject
    run:npmrunbuild
    
    ```
    
    프로덕션 빌드 수행
    
    빌드 오류 조기 검출 목적
    
    ---
    
    ## 전체 Workflow 흐름 요약
    
    이벤트 발생
    
    Workflow 트리거
    
    Runner 할당
    
    Job 실행
    
    Step 순차 실행
    
    성공 또는 실패 상태 기록
    
    ---
    
    ## Secrets 사용 예제
    
    ```yaml
    -name:Usesecret
    run:echo${{secrets.DB_PASSWORD}}
    
    ```
    
    GitHub Secrets 참조 방식
    
    민감 정보 코드 외부 관리
    
    로그 출력 시 자동 마스킹
    
    ---
    
    ## 환경 변수 정의
    
    ```yaml
    env:
    NODE_ENV:production
    
    ```
    
    Workflow 단위 환경 변수
    
    모든 Job과 Step에서 접근 가능
    
    ---
    
    ## Job 간 의존성 설정
    
    ```yaml
    jobs:
    build:
    runs-on:ubuntu-latest
    
    deploy:
    needs:build
    runs-on:ubuntu-latest
    
    ```
    
    build Job 성공 이후 deploy Job 실행
    
    CI → CD 순차 파이프라인 구성
    
    실패 전파 구조
    
    ---
    
    ## 조건부 실행
    
    ```yaml
    if:github.ref=='refs/heads/main'
    
    ```
    
    브랜치 기반 실행 조건
    
    main 브랜치에서만 Step 또는 Job 실행
    
    ---
    
    ## 캐시 사용 예제
    
    ```yaml
    -uses:actions/cache@v4
    with:
    path:~/.npm
    key:npm-${{hashFiles('package-lock.json')}}
    
    ```
    
    의존성 캐시 저장
    
    lock 파일 기반 캐시 키 생성
    
    빌드 시간 단축 목적
    
    ---
    
    ## 아티팩트 저장
    
    ```yaml
    -uses:actions/upload-artifact@v4
    with:
    name:build-output
    path:dist/
    
    ```
    
    빌드 결과물 저장
    
    Workflow 실행 결과 보존
    
    CD 단계 또는 디버깅 활용
    
    ---
    
    ## Docker 빌드 및 푸시 예제
    
    ```yaml
    -name:BuildDockerimage
    run:dockerbuild-tmyapp:latest.
    
    -name:PushDockerimage
    run:dockerpushmyapp:latest
    
    ```
    
    컨테이너 이미지 생성
    
    레지스트리 푸시
    
    컨테이너 기반 CD 연결
    
    ---
    
    ## GitHub Actions 핵심 특징 요약
    
    YAML 기반 선언형 구성
    
    이벤트 기반 자동 실행
    
    Job 병렬 처리 구조
    
    Action 단위 재사용성
    
    GitHub 생태계와의 강한 결합
    
- Reverse Proxy
    
    # Reverse Proxy
    
    Reverse Proxy는 클라이언트와 실제 서버 사이에 위치하여, 클라이언트의 요청을 대신 받아 내부 서버로 전달하는 서버를 의미한다.
    
    클라이언트 입장에서는 Reverse Proxy가 곧 서버처럼 보이며, 실제 백엔드 서버의 존재나 구조는 알 필요가 없다.
    
    이 구조의 핵심은 **서버를 보호하면서 트래픽을 제어**하는 데 있다.
    
    클라이언트가 직접 백엔드 서버에 접근하지 않고, 반드시 Reverse Proxy를 거쳐 접근하게 만드는 구조다.
    
    ---
    
    ## Reverse Proxy 기본 동작 흐름
    
    클라이언트가 웹 요청을 보낸다.
    
    요청은 가장 먼저 Reverse Proxy에 도달한다.
    
    Reverse Proxy는 요청을 분석한 뒤, 내부에 있는 적절한 서버로 요청을 전달한다.
    
    백엔드 서버의 응답은 다시 Reverse Proxy를 거쳐 클라이언트에게 전달된다.
    
    이 과정에서 클라이언트는 내부 서버의 IP, 포트, 개수 등을 전혀 인지하지 못한다.
    
    ---
    
    ## Forward Proxy와의 차이
    
    Forward Proxy는 클라이언트 쪽에 위치한 대리 서버다.
    
    주 목적은 클라이언트 보호, 접근 제어, 익명성 제공이다.
    
    Reverse Proxy는 서버 쪽에 위치한 대리 서버다.
    
    주 목적은 서버 보호, 트래픽 분산, 성능 최적화다.
    
    즉,
    
    Forward Proxy는 “누가 나가느냐”를 숨기고
    
    Reverse Proxy는 “누가 받느냐”를 숨긴다.
    
    ---
    
    ## Reverse Proxy를 사용하는 이유
    
    백엔드 서버를 외부에 직접 노출할 경우 보안 위험이 커진다.
    
    트래픽이 증가하면 서버 확장이나 분산 처리가 필요해진다.
    
    HTTPS 처리, 인증서 관리, 공통 보안 정책을 서버마다 적용하기 어렵다.
    
    이 문제들을 한 지점에서 해결하기 위해 Reverse Proxy를 사용한다.
    
    ---
    
    ## 주요 역할
    
    Reverse Proxy는 단순히 요청을 전달하는 것 이상을 수행한다.
    
    요청 라우팅 기능을 통해 URL이나 도메인에 따라 서로 다른 서버로 요청을 분기할 수 있다.
    
    로드 밸런싱을 통해 여러 서버에 트래픽을 분산시킬 수 있다.
    
    SSL/TLS 종료 지점으로 동작하여 HTTPS 처리를 담당할 수 있다.
    
    정적 리소스를 캐싱하여 응답 속도를 향상시킬 수 있다.
    
    백엔드 서버를 외부 공격으로부터 보호할 수 있다.
    
    ---
    
    ## Nginx 기반 Reverse Proxy 예제
    
    가장 기본적인 Reverse Proxy 설정은 다음과 같다.
    
    ```
    server {
    listen80;
    
    location / {
    proxy_pass http://localhost:3000;
    proxy_set_header Host$host;
    proxy_set_header X-Real-IP$remote_addr;
    proxy_set_header X-Forwarded-For$proxy_add_x_forwarded_for;
        }
    }
    
    ```
    
    이 설정에서 Nginx는 80번 포트로 들어오는 요청을 수신한다.
    
    모든 요청은 `localhost:3000`에서 실행 중인 백엔드 서버로 전달된다.
    
    클라이언트의 실제 IP와 원본 Host 정보는 헤더를 통해 백엔드로 전달된다.
    
    ---
    
    ## Header 전달이 필요한 이유
    
    Reverse Proxy를 거치면 백엔드 서버 입장에서는 요청이 프록시에서 온 것처럼 보인다.
    
    이 때문에 실제 클라이언트 정보를 전달해주지 않으면 로그, 인증, 보안 처리에 문제가 생긴다.
    
    `X-Real-IP`는 실제 클라이언트 IP 전달 목적이다.
    
    `X-Forwarded-For`는 프록시를 거친 경로 정보 전달 목적이다.
    
    `Host` 헤더는 도메인 기반 라우팅을 위해 필요하다.
    
    ---
    
    ## 로드 밸런싱 예제
    
    Reverse Proxy는 여러 백엔드 서버 앞단에서 트래픽을 분산할 수 있다.
    
    ```
    upstream backend {
    server localhost:3000;
    server localhost:3001;
    }
    
    server {
    listen80;
    
    location / {
    proxy_pass http://backend;
        }
    }
    
    ```
    
    이 구조에서는 요청이 3000번과 3001번 서버로 분산된다.
    
    이를 통해 서버 부하 분산과 장애 대응이 가능해진다.
    
    ---
    
    ## Reverse Proxy와 Load Balancer 관계
    
    Reverse Proxy는 개념이다.
    
    Load Balancer는 기능이다.
    
    Reverse Proxy는 요청을 대신 받아 전달하는 구조를 의미하고,
    
    그 과정에서 로드 밸런싱 기능을 포함할 수 있다.
    
    실무에서는 Reverse Proxy = Load Balancer 역할을 동시에 수행하는 경우가 많다.
    
    ---
    
    ## Reverse Proxy와 API Gateway 관계
    
    Reverse Proxy는 요청 전달과 트래픽 제어 중심이다.
    
    API Gateway는 여기에 인증, 인가, Rate Limit, 로깅 기능이 추가된 개념이다.
    
    마이크로서비스 환경에서는
    
    Reverse Proxy → API Gateway → 내부 서비스 구조로 확장되는 경우가 많다.
    
    ---
    
    ## Kubernetes 환경에서의 Reverse Proxy
    
    Kubernetes에서는 Ingress Controller가 Reverse Proxy 역할을 수행한다.
    
    외부 트래픽의 진입 지점 역할을 하며, Service 단위로 트래픽을 전달한다.
    
    Nginx Ingress, Traefik, Envoy 등이 대표적이다.
    
    ---
    
    ## 장점과 고려 사항
    
    장점으로는 보안 강화, 트래픽 관리 용이성, 확장성 확보가 있다.
    
    백엔드 서버 구조 변경 시 클라이언트에 영향이 없다.
    
    단점으로는 Reverse Proxy 장애 시 전체 서비스에 영향이 있다는 점이다.
    
    따라서 고가용성 구성과 모니터링이 중요하다.
    
- HTTPS
    
    # HTTPS
    
    HTTPS는 HTTP에 TLS(SSL) 암호화 계층을 추가한 통신 방식이다.
    
    클라이언트와 서버 간에 오가는 데이터가 암호화되어 전송되며, 중간에서 내용을 가로채더라도 해석이 불가능하다.
    
    HTTP는 평문 통신이기 때문에 패킷 스니핑, 중간자 공격에 취약하다.
    
    HTTPS는 이를 해결하기 위해 암호화, 인증, 무결성을 제공한다.
    
    ---
    
    ## HTTPS가 제공하는 핵심 요소
    
    암호화: 전송 데이터의 기밀성 보장
    
    인증: 서버가 신뢰할 수 있는 대상인지 검증
    
    무결성: 데이터 위변조 여부 검증
    
    ---
    
    ## HTTPS 동작 방식
    
    클라이언트가 HTTPS 요청을 보낸다.
    
    서버는 인증서를 클라이언트에게 전달한다.
    
    클라이언트는 인증서의 신뢰성을 검증한다.
    
    대칭키를 안전하게 교환한 뒤 암호화 통신을 시작한다.
    
    실제 데이터 전송은 대칭키 암호화를 사용한다.
    
    ---
    
    ## 인증서 개념
    
    인증서는 서버의 공개키와 신원 정보를 포함한 전자 문서다.
    
    신뢰 기관(CA)에 의해 서명된다.
    
    브라우저는 CA 목록을 통해 인증서의 신뢰성을 판단한다.
    
    ---
    
    ## HTTPS와 Reverse Proxy
    
    실무에서는 HTTPS 처리를 Reverse Proxy에서 담당하는 경우가 많다.
    
    이를 SSL/TLS 종료라고 한다.
    
    백엔드 서버는 HTTP로 통신하고, 외부와의 통신만 HTTPS로 유지한다.
    
    ---
    
    ## Nginx HTTPS 설정 예시
    
    ```
    server {
    listen443 ssl;
    server_name example.com;
    
    ssl_certificate     /etc/ssl/cert.pem;
    ssl_certificate_key /etc/ssl/key.pem;
    
    location / {
    proxy_pass http://localhost:3000;
        }
    }
    
    ```
    
    443 포트에서 HTTPS 요청 수신
    
    인증서와 개인키 설정
    
    내부 서버로 요청 전달
    
    ---
    
    ## HTTP → HTTPS 리다이렉트
    
    ```
    server {
    listen80;
    return301 https://$host$request_uri;
    }
    
    ```
    
    모든 HTTP 요청을 HTTPS로 강제 전환
    
    보안 강화를 위한 필수 설정
    
    ---
    
    ## HTTPS 사용 이유
    
    데이터 도청 방지
    
    서버 신뢰성 확보
    
    로그인, 결제 등 민감 정보 보호
    
    현대 웹 서비스의 기본 요구 사항