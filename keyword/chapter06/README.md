- ORM
  ORM은 객체-관계 매핑으로, 객체 지향 프로그래밍 언어의 객체와 관계형 데이터베이스의 테이블을 연결해주는 추상화 계층을 말한다.
  즉 SQL 쿼리를 직접 작성하지 않고, 코드 내에서 데이터베이스를 객체로 다루며 직관적으로 접근할 수 있다는 장점이 있다.
  ### 장점
  - SQL을 직접 작성하는 부담이 줄고, 코드 가독성 및 일관성이 뛰어나다.
  - DB 스키마 변경에 대한 유지보수가 용이하다. ORM 프레임워크가 자동으로 변화에 대응해준다.
  - 개발 생산성이 향상되고, 비즈니스 로직에 집중할 수 있다.
  - DBMS에 대한 종속성이 줄며, 다양한 데이터베이스로 쉽게 이식 가능하다.
  - 객체 간의 관계를 코드에서 직관적으로 관리할 수 있다.
  ### 단점
  - 대용량 트랜잭션이나 복잡한, 성능이 중요한 SQL은 ORM만으로 최적화하기 어렵다.
  - 모든 데이터베이스 기능을 지원하지 않아, 특수하거나 고도화된 쿼리는 직접 SQL로 처리해야 한다.
  - 프로젝트의 규모나 복잡성이 증가하면 ORM 구조가 설계 및 관리 측면에서 어려워질 수 있다.
  - DB와 객체 구조에 대한 높은 이해가 필요하다. 설계 실패 시 성능 저하나 데이터 불일치가 발생할 수 있다.
- Prisma 문서 살펴보기
  - ex. Prisma의 Connection Pool 관리 방법
    ### 쿼리 엔진이 커넥션 풀 이용하는 과정
    1. 쿼리 엔진이 connection pool size와 timeout 설정을 통해 pool을 인스턴스화한
    2. 쿼리 엔진은 하나의 connection을 생성해 연결 풀에 추가
    3. 쿼리가 들어오면 쿼리 엔진은 쿼리 처리하기 위해 연결을 예약
    4. 연결 풀에 사용 가능한 유휴 연결이 없으면 쿼리 엔진은 추가 데이터베이스 연결을 열고 데이터베이스 연결 수가 정의된 제한에 도달할 때까지 연결 풀에 추가 `connection_limit`
    5. 쿼리 엔진이 풀에서 연결 예약할 수 없을 때, 쿼리는 메모리의 FIFO 큐에 추가됨. (순서대로 처리하기 위해)
    6. 쿼리 엔진이 시간 제한 전에 큐에 있는 쿼리 처리할 수 없는 경우 해당 쿼리에 대한 오류 코드와 함계 예외 발생, 큐에있는 다음 쿼리로 넘어감
    ### 연결 풀 크기
    default 크기: cpu 개수 \* 2 + 1
    혹은 connection_limit 매개변수를 통해 명시적으로 지정 가능
    ```scheme
    datasource db {
      provider = "postgresql"
      url      = "postgresql://johndoe:mypassword@localhost:5432/mydb?connection_limit=5"
    }
    ```
    ### 연결 풀 시간 초과
    기본 제한시간은 10초
    이 또한 명시적으로 설정 가능
    ```jsx
    datasource db {
      provider = "postgresql"
      url      = "postgresql://johndoe:mypassword@localhost:5432/mydb?connection_limit=5&pool_timeout=2"
    }
    // 혹은 0으로 설정하여 연결 풀 시간초과를 비활성화할 수 있다.
    ```
  - ex. Prisma의 Migration 관리 방법
    ### 마이그레이션 관리란?
    디비 스키마의 변경 이력을 체계적으로 추적하고 관리하며, 시간이 지남에 따라 디비 구조를 안정적으로 업데이트하고 동기화하는 프로세스
    ### 마이그레이션 관리가 필요한 이유
    1. 버전 관리 및 추적: 특정 시점으로 롤백 가능
    2. 팀 협업: 여러 개발자가 동시에 디비 변경할 때 충돌 없이 변경사항 통합/적용 가능
    3. 환경 동기화: 개발, 테스트, 운영 환경 간의 디비 스키마를 일관성 있게 유지
    ### Prisma에서 마이그레이션 관리하는 법
    schema.prisma 파일의 모델 정의를 기반으로 관리
    - prisma/migrations 디렉토리에 타임스탬프가 붙은 SQL 파일 형태로 저장
    - 디비 내부에 *prisma*migrations라는 특별 테이블 만들어, 어떤 마이그레이션 적용됐는지 기록
    - **프로세스 : 프리즈마 모델 수정 > prisma migrate dev > sql 파일 생성 및 디비 적용**
    ```bash
    npx prisma migrate dev // 개발 환경에서 스키마 변경을 감지하고, 마이그레이션 파일 생성 및 즉시 적용.
    npx prisma migrate deploy // 배포 환경에서 이미 생성된 마이그레이션 파일을 안전하게 적용.
    npx prisma migrate reset // 데이터베이스를 초기화(모든 데이터 삭제)하고 마이그레이션 기록을 재설정.
    ```
- ORM(Prisma)을 사용하여 좋은 점과 나쁜 점
  ## 장점
  - 생산성 향상 및 직관적인 코드: SQL 쿼리 대신 프로그래밍 언어의 메서드를 사용해 데이터를 조작할 수 있어 개발 속도가 빠르고 코드가 객체 지향적이며 직관적
  - 유지보수 용이성: 매핑 정보가 명확하고 코드가 독립적으로 작성되어 재사용성이 높고, SQL이 코드에 분산되지 않아 유지보수가 편리함
  - DBMS 종속성 감소: 대부분의 ORM은 여러 데이터베이스(DB)를 지원하여, DB를 교체할 때 코드 수정 범위를 최소화
  - 보안 강화: SQL 인젝션과 같은 보안 공격을 방지하는 Prepared Statement 사용
  ## 단점
  - 성능 저하 가능성: 복잡한 쿼리(JOIN, 서브 쿼리 등)의 경우, ORM이 생성하는 SQL이 최적화되지 않아 직접 작성한 Raw SQL보다 성능이 떨어질 수 있음
  - 학습 곡선과 설계 난이도: ORM의 내부 동작 원리(N+1 문제 등)를 충분히 이해하지 못하면 오히려 성능 저하 및 데이터 일관성 문제 발생 위험
  - 완벽한 지원의 한계: 모든 상황을 ORM만으로 해결하기는 어려우며, 복잡하거나 극한의 성능이 필요한 쿼리는 결국 Raw SQL이나 저장 프로시저(Stored Procedure)를 사용해야 함
  ## N+1 문제
  데이터베이스에서 연관된 데이터를 가져올 때, 하나의 쿼리로 전체 데이터를 가져오는 대신
  하나의 메인 쿼리 + 관련 데이터를 위한 N개의 추가 쿼리가 발생하는 문제
  ### **include를 사용해서 해결하기**
  Prisma는 기본적으로 lazy loading이 아니라 eager loading을 지원
  include 옵션을 쓰면 관계 데이터를 한 번에 JOIN해서 가져올 수 있음
  ```tsx
  const users = await prisma.user.findMany({
    include: {
      posts: true, // posts를 한 번에 가져옴
    },
  });
  ```
- 다양한 ORM 라이브러리 살펴보기
  ## Prisma
  - TypeScript 완벽 지원, 타입 자동 생성
  - 선언적 스키마(shema.prisma)로 DB 구조 정의
  - 빠른 쿼리, 자동 마이그레이션, 직관적인 API
  - 장점: DX(개발자 경험) 최고, 타입 안정성 탁월
  - 단점: 복잡한 커스텀 SQL 쿼리에는 다소 제약
  ## TypeORM
  - NestJS 공식 문서에서도 추천되는 전통적인 ORM
  - Active Record / Data Mapper 두 패턴 모두 지원
  - 데코레이터 기반 (@Entity(), @Column() 등)
  ## Sequelize
  - 가장 오래되고 널리 사용된 Node.js ORM
  - Promise 기반 API, 트랜잭션/연관관계 잘 지원
  - 장점: 안정적, 문서 많음
  - 단점: 타입스크립트 지원이 부족함 (최근 개선 중)
  ## Objection.js
  - Knex.js 기반 ORM (쿼리 빌더 + 모델 시스템)
  - SQL에 가까운 컨트롤을 제공하면서도 ORM의 장점 유지
  - 타입스크립트 호환성 좋음
- 페이지네이션을 사용하는 다른 API 찾아보기
  - ex. https://docs.github.com/en/rest/using-the-rest-api/using-pagination-in-the-rest-api?apiVersion=2022-11-28
  - ex. https://developers.notion.com/reference/intro#pagination
  ## 링크 헤더 기반 페이지네이션
  - http 헤더에 다음, 이전, 첫번째, 마지막 페이지로이동할 수 있는 완전한 URL 링크 제공
  - 사용되는 매개변수
    - page, before/after, since : link 헤더에 포함된 url 자체를 사용해 요청
    - per_page: 한 페이지에 반환되는 결과 수 제어
  - 응답 시 주요 필드
    - link 헤더는 응답헤더에 포함되고, URL을 제공한다.
  - 작동 방식
  1. 요청
  2. 응답 헤더에서 Link 헤더 확인
  3. rel=”next”에 해당하는 URL을 사용해 다음 요청
  4. 반복
