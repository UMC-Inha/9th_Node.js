## 미션 링크
https://github.com/rbdus0715/UMC_NodeJS_Practice


## 시니어 미션
- OpenAPI 버전 별 특징 및 주요 차이점에 대해 찾아 정리해주세요. (2.0, 3.0, 3.1)
    
    
    # **OpenAPI 2.0 (Swagger 2.0)**
    
    > 2014 / Swagger 시절 사용되던 버전
    > 
    > 
    > 아직도 많은 레거시 프로젝트에서 사용됨
    > 
    
    ### **특징**
    
    - Swagger 사양이 OpenAPI로 넘어가기 전 마지막 버전
    - **스키마 구조 단순하지만 제약이 많음**
    - **"forms", "multipart", "file 업로드" 등을 명시적으로 지원하지 않음**
    - 항상 **JSON Schema Draft 04 기반**
    
    ### **구조적 한계**
    
    - Request body 정의 방식이 비일관적
        
        → `parameters` 안에 `in: body` 로 정의해야 함
        
    - 하나의 API에 **하나의 request body만 허용**
    - Content-type 별 분리를 지원하지 않음
    
    ### **파일 업로드에 제한**
    
    - `type: file` 이라는 **OpenAPI 2.0에만 존재하는 특수 타입** 필요
    
    ---
    
    # **OpenAPI 3.0**
    
    > 2017 / Swagger 이름에서 벗어나면서 대규모 리디자인
    > 
    > 
    > 실제 현재 업계에서 가장 널리 사용되는 표준
    > 
    
    ### ⭐ **가장 큰 변화**
    
    ### 1) **Request Body 구조 개편**
    
    OpenAPI 2.0의 복잡했던 구조 삭제
    
    → **독립된 `requestBody` 섹션 도입**
    
    ```yaml
    requestBody:
      content:
        application/json:
          schema:
    
    ```
    
    Content-type 별로 request body 구분 가능 🥇
    
    ---
    
    ### 2) **Content negotiation(미디어 타입 분리) 정식 지원**
    
    다음과 같이 JSON, XML, multipart 등 각각 정의 가능:
    
    ```yaml
    content:
      application/json:
      application/xml:
      multipart/form-data:
    
    ```
    
    ---
    
    ### 3) **Components 도입**
    
    재사용 가능한 스키마를 정의하는 **components** 섹션 추가
    
    - schemas
    - responses
    - parameters
    - requestBodies
    - headers
    - securitySchemes 등
    
    → API 문서 구조화가 훨씬 쉬워짐
    
    ---
    
    ### 4) **File 업로드 방식 변경**
    
    `type: file` 삭제 → **`type: string`, format: binary**
    
    ```yaml
    type: string
    format: binary
    
    ```
    
    ---
    
    ### 5) **JSON Schema 호환되지만 완전하지는 않음**
    
    - OpenAPI 3.0은 JSON Schema Draft 05 기반을 부분적으로 사용
    - **100% 일치하지 않아** 변환 과정이 필요함
    
    ---
    
    # **OpenAPI 3.1**
    
    > 2021 / JSON Schema와 완전 호환되는 첫 버전
    > 
    > 
    > 최신 버전이지만 실제 업계 도입률은 아직 3.0보다 낮음
    > 
    
    ### ⭐ **가장 큰 변화**
    
    ### 1) **JSON Schema 2020-12와 완전 호환**
    
    3.0에서는 "비슷하지만 동일하지 않은" JSON Schema
    
    → 3.1에서는 **정식 JSON Schema 사용 가능**
    
    예: `if/then/else`, `unevaluatedProperties`, `const`, `contains` 등 사용 가능
    
    ---
    
    ### 2) **Base spec가 OpenAPI Schema Object가 아니라 JSON Schema 자체를 사용**
    
    → 변환 불필요
    
    → 타입 표현력 증가
    
    → 복잡한 validation 가능
    
    ---
    
    ### 3) **Semantic Versioning 지원 (semver)**
    
    3.1부터 명시적으로 "규칙적 버전 체계"를 따름
    
    ---
    
    ### 4) **nullable 제거**
    
    3.0:
    
    ```yaml
    nullable: true
    
    ```
    
    3.1:
    
    ```yaml
    type: [string, null]
    
    ```
    
    → JSON Schema 방식 그대로 사용
    
    ---
    
    ### 5) **webhooks 추가**
    
    웹훅 자료 구조를 표준에 포함 (RFC 기반)
    
    ---
    
    ### 6) **`servers`가 optional**
    
    3.0: `servers` 필수
    
    3.1: 없어도 됨 (구조 간소화)
    
    ---
    
    # **버전별 핵심 비교 요약**
    
    | 항목 | OpenAPI 2.0 | OpenAPI 3.0 | OpenAPI 3.1 |
    | --- | --- | --- | --- |
    | 이름 | Swagger 2.0 | OpenAPI 3.0 | OpenAPI 3.1 |
    | Request Body | `parameters` 내 `in: body` | `requestBody` 독립 | 동일 |
    | JSON Schema | Draft 04 | 부분 호환 | **완전 호환 (2020-12)** |
    | Media Types | 제한적 | `content:` 정식 도입 | 동일 |
    | File upload | `type: file` | `type: string, format: binary` | 동일 |
    | nullable | 지원 안함 | `nullable: true` | JSON Schema 방식 (`type: [string, null]`) |
    | Webhooks | 없음 | 없음 | 정식 포함 |
    | Servers 필수 여부 | 없음 | 필수 | 선택 |
    | 사용 빈도 | 레거시 | **가장 널리 사용됨** | 최신, 점차 확대 중 |
    
    ---
    
    # **현재 어떤 버전을 쓰는 게 베스트?**
    
    ### ✔ 거의 모든 상황 → **OpenAPI 3.0**
    
    - Swagger UI, Swagger Editor, Spring, NestJS 등에서 가장 안정적 지원
    - 생태계 가장 넓음
    
    ### ✔ 고급 JSON Schema 검증 필요 → **OpenAPI 3.1**
    
    - Node.js 기반 신 프로젝트라면 매우 추천
    - Zod / JSON Schema 기반 API에서는 특히 베스트
    - 하지만 아직 일부 툴(예: 일부 구버전 Swagger UI)은 완전 지원 X
    
    ### ✔ 레거시 프로젝트 → **2.0 유지 후 점진적 전환**