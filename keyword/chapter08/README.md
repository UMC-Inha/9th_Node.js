- Swagger
  https://yozm.wishket.com/magazine/detail/2195/
  ***
  ## Swagger가 등장하기 전 문제점
  ![image.png](attachment:af186e11-e137-4b93-997a-51666c98b56c:image.png)
  - 프론트엔드와 백엔드가 **문서(노션/구글독스/엑셀 등)** 로 API 정보를 주고받음
  - 백엔드가 URL, Request, Response를 **직접 수기로 작성**해야 함 → 오류·누락 많음
  - API 변경 시 문서를 계속 수정해야 해서 **생산성 낮음**
  - 프론트는 Postman/cURL로 **직접 테스트**해야 하므로 비효율
  - 결국 의사소통과 유지보수가 매우 어려움
  ***
  ## Swagger의 등장
  - API 문서를 **자동으로 생성**하므로 백엔드가 문서 작성할 필요 없음
  - Swagger UI에서 **API를 바로 테스트**할 수 있음
  - 파라미터, 스키마, 예시 등을 **직관적으로 제공**
  - API **버전 관리**가 수월함
  - 다양한 API를 **한 UI에 통합 관리** 가능
  - Spring Boot와 통합하면 **개발자는 API 개발에만 집중**할 수 있음 (어노테이션 기반)
  ***
  ## Swagger 구성 방식 2가지 (Node.js 기준)
  ### (1) **YAML 파일로 작성해 구성하는 방식**
  ![image.png](attachment:b48d3b48-9a09-4e5d-8ec5-061200726186:image.png)
  ### ✔️ 언제 쓰는 방식인가?
  - Swagger 문서를 **YAML/JSON으로 따로 작성**
  - Swagger UI 서버와 API 서버가 **서로 다른 서버**일 때 주로 사용됨
  - API Gateway나 문서 서버를 별도 운영할 때 적합
  ### ✔️ 특징
  - 문서를 별도 파일(`openapi.yaml`)로 관리
  - API가 많으면 YAML이 커지고 동기화도 귀찮음
  - 대신 프론트에서 문서만 가져다 쓰기 편함
  ### ✔️ 구성 요소
  - `openapi`: 문서 사양 버전
  - `info`: 제목, 버전, 설명
  - `servers`: 실제 API 서버 목록
  - `paths`: 엔드포인트 + 메서드별 요청/응답
  - `components`: 스키마, 파라미터, 보안 스키마 등 재사용 요소
  ### ✔️ Node.js 적용 예시
  ```tsx
  app.use(
    "/docs",
    swaggerUi.serve,
    swaggerUi.setup(null, {
      swaggerOptions: {
        url: "/openapi.yaml",
      },
    })
  );
  ```
  → YAML을 따로 관리하고 싶은 팀/회사에서 선호
  ***
  ### (2) **Node.js(Express) 소스코드 기반 자동 생성 방식**
  ![image.png](attachment:d3b37d80-6b1b-466d-98ba-d7ca0c371e1e:image.png)
  Spring에서 `@EnableSwagger2`를 쓰는 것처럼, Node에서는 **swagger-autogen** 또는 **swagger-jsdoc** 방식으로 코드 기반에서 스펙을 자동 생성함.
  ### ✔️ 주요 라이브러리
  - **swagger-ui-express**: Swagger UI 제공
  - **swagger-autogen**: 라우트 기반 자동 문서 생성
  - **swagger-jsdoc**: JSDoc 주석 기반 자동 생성
  ### ✔️ 장점
  - API 라우트와 Swagger 문서가 **한 코드베이스에서 함께 유지됨**
  - 엔드포인트 수정 시 문서도 자동 반영
  - 프론트/백 모두 같은 URL에서 문서를 확인 가능
  - 수동 YAML 관리보다 작업량 감소
- OpenAPI
  https://apidog.com/kr/blog/swagger-vs-openapi-5/
  ***
  ## 1. 먼저 쉽게 이야기 해보자면
  **< Swagger = OpenAPI를 쓰기 쉽게 만든 _툴 세트 >_**
  - **OpenAPI = API 문서 작성 규칙**
    - 맞춤법, 문법 같은 것
  - **Swagger = 그 규칙으로 문서를 만들고 보여주는 편집기/툴**
    - 한글, MS Word 같은 것
      ⇒ OpenAPI는 **표준 문서 포맷**이다
      ⇒ Swagger UI는 **OpenAPI 문서를 보여주는 도구**이다
  ***
  ## 2. Swagger란 무엇인가?
  - 2011년에 처음 출시된 **오픈 소스 소프트웨어 프레임워크**
  - RESTful API 개발을 위해 설계되었으며:
    - API 설계
    - 문서화
    - 구축
    - 테스트
      등을 쉽게 할 수 있게 해주는 **도구 모음**
  - 직관적인 UI로 API를 **시각화하고 상호작용** 가능
  - 수동 코딩 없이 인터랙티브한 API 문서를 자동 생성
  - 다양한 언어에서 **클라이언트 SDK / 서버 스텁 자동 생성** 기능 제공
  ***
  ## 3. OpenAPI란 무엇인가?
  - 이전 이름: **Swagger 2.0**
  - Google, IBM, Microsoft 등이 포함된 **OpenAPI Initiative**가 관리하는 **표준 사양**
  - RESTful API를 설명하기 위한 **오픈 표준**
  - JSON 또는 YAML 기반 → **기계 판독 가능**
  - Swagger의 기능을 확장하여:
    - 인증
    - 오류 처리
    - 데이터 검증
      같은 고급 기능까지 정의 가능
  - API를 **문서화·표준화·상호운용 가능**하게 만드는 데 중점
  - 단순 Swagger의 이름 변경이 아니라, **더 큰 표준화 운동**
  ***
  ## 3. Swagger vs OpenAPI — 4가지 주요 차이점
  ### ① 기원
  - **Swagger**: 2011년 Tony Tam(Reverb Technologies)이 개발한 _툴 프레임워크_
  - **OpenAPI**: Swagger 2.0을 기반으로 만들어진 _표준 사양_, 산업 컨소시엄이 관리
  ### ② 초점
  - **Swagger**: API 설계·문서화·개발·테스트를 위한 **도구세트**
  - **OpenAPI**: REST API를 설명하기 위한 **표준 형식(Spec)** 제공
  ### ③ 커뮤니티
  - **Swagger**: 오래되고 넓은 생태계, 플러그인/리소스 많음
  - **OpenAPI**: 영향력 있는 기업들이 참여하는 성장 중 커뮤니티
  ### ④ 프로그래밍 언어
  - **Swagger**: 다양한 언어의 클라이언트/서버 코드 생성기 제공
  - **OpenAPI**: 언어 독립적, JSON/YAML만 있으면 어떤 언어든 사용 가능
  ***
  ## 5. 결론
  - Swagger = **툴 세트**
  - OpenAPI = **표준 사양**
  ```jsx
  openapi: 3.0.0
  paths:
    /users:
      get:
        summary: 유저 목록 조회
        responses:
          200:
            description: 성공
  ```
  즉 **언어에 종속되지 않는 문서 형식** 이라서
  Node, Spring, Django, Go, Rust 등 어떤 서버든 문서 구조는 동일하다.
  - 규격을 맞춘거라 다음과 같은 이점들이 있다.
    ㅊ문서 자동화 → 휴먼에러 제거
    ✔ 팀 협업 속도 증가
    **✔ 서버/클라 SDK(Software Development Kit) 자동 생성**
    ✔ API 유지보수 편해짐
  ***
- OpenAPI Component
  https://velog.io/@hyex/Swagger-Components-Section-OpenAPI3
  https://swagger.io/docs/specification/v3_0/components/
  ***
  ## ✔ **Components는 ‘재사용 가능한 정의 모음’**
  **[ OpenAPI의 components 구성 ]**
  가장 많이 쓰는 건 **schemas**와 **responses**.
  | 구분 | 역할 |
  | ----------------- | ------------------------------------ |
  | `schemas` | 객체 구조(=TypeScript 타입 같은 것) |
  | `responses` | 공통 응답 구조 |
  | `parameters` | path/query/header 파라미터 공통 정의 |
  | `requestBodies` | 공통 request body 구조 |
  | `securitySchemes` | JWT, API Key 같은 인증 설정 |
  | `examples` | 요청/응답 예시 |
  | `headers` | 공통 헤더 정의 |
  | `links` | API 응답→다음 API 연결 |
  | `callbacks` | webhook 같은 비동기 콜백 API |
  이런 것들을 전부 모아놓는 **전역 저장소(global reusable definition storage)**
  ⇒ 모든 항목은 선택 optional. ⇒ 필요한것만 넣으면 된다
  ⇒ 이걸 다른 API 엔드포인트에서 **$ref** 로 재사용
  ***
  ## 📌 왜 필요한가?
  예를 들어 *User*라는 객체를 10개 API에서 응답으로 사용한다고 해보자.
  이걸 paths에 매번 적으면?
  - 중복
  - 유지보수 지옥
  - API 하나만 바뀌어도 10개를 수정해야 함
    → 그래서 재사용 가능하게 **components.schemas.User**로 따로 정의해두는 것.
  ***
  ## 예시.
  **[ 많이 쓰이는 components.schemas ]**
  ```jsx
  components: schemas: User: type: object;
  properties: id: type: number;
  name: type: string;
  email: type: string;
  ```
  그러면 다른 API에서 이렇게 쓴다:
  ```jsx
  responses:
    200:
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/User"
  ```
  ⇒ 재사용 가능
  ⇒ 변경사항 한 번에 반영
  ## 결론적으로 중복도 줄고, 문서의 유지보수가 매우 편해진다.
