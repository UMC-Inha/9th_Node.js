- ORM
  **ORM 이란? 다시 말해서..**
  ORM (object-relational mapping) 이란 객체(클래스)와 관계(관계형 데이터베이스) 와의 설정을 의미한다.
  객체 지향 프로그래밍은 클래스를 사용하고 관계형 데이터베이스는 테이블을 사용한다.
  여기서 객체 모델과 관계형 모델 간에 불일치가 존재하는데 이 객체간의 관계를 바탕으로 SQL 을 자동 생성하여 불일치를 해결하는 것이 ORM 이다.
  ```jsx
  Object <= 매핑 => DB데이터
  ```
  에서 매핑의 역할을 하는 것이 ORM이라 할 수 있다.
  ***
  요약하자면,
  객체와 RDB의 데이터를**자동으로 매핑해주는 기술**로, SQL을 직접 작성하지 않아도 데이터베이스를 다룰 수 있게 해준다.
- Prisma 문서 살펴보기

  - ex. Prisma의 Connection Pool 관리 방법
    참고: https://www.prisma.io/docs/postgres/database/connection-pooling

    ***

    ## Prisma의 Connection Pool 관리 방식 정리

    ## 1. 개요

    Prisma ORM은 데이터베이스와의 연결을 효율적으로 관리하기 위해
    내부적으로 Connection Pool(커넥션 풀)을 사용한다.
    애플리케이션이 Prisma Client를 통해 처음으로 DB에 접근할 때,
    `$connect()` 메서드를 호출하거나 첫 쿼리가 실행되면
    자동으로 커넥션 풀이 생성된다.
    이 풀은 한 번 생성되면, 이후의 모든 쿼리에 재사용되어
    매 요청마다 새로운 DB 연결을 여는 오버헤드를 줄인다.
    **결론**
    ⇒ 일반적인 서버 환경에서는 Prisma가 커넥션 풀을 자동 관리하므로 따로 신경 쓸 필요가 없다. 단, PrismaClient는 앱 전체에서 딱 하나만 한번 생성해야한다.
    ⇒ 하나의 PrismaClient를 전역에서 재사용해야 내부 커넥션 풀이 올바르게 유지된다.

    ***

    ## 2. 작동 원리

    1. Prisma Query Engine이 애플리케이션 구동 시점에 커넥션 풀을 초기화한다.
    2. 최초 1개의 DB 연결을 생성하여 풀에 등록한다.
    3. 쿼리가 들어오면 풀에서 유휴(Idle) 커넥션을 할당하여 실행한다.
    4. 풀에 여유 커넥션이 없으면 새 커넥션을 만들어 추가한다.
    5. 이때 전체 커넥션 수가 `connection_limit` 값을 초과하지 않도록 제어된다.
    6. 만약 모든 커넥션이 사용 중이면, 새 쿼리는 **FIFO(First In First Out)** 큐에 대기된다.
    7. 지정된 대기 시간(`pool_timeout`) 내에 커넥션을 얻지 못하면,

       Prisma는 `P2024` 에러를 발생시키고 해당 쿼리를 건너뛴다.

    8. 쿼리가 종료되면 커넥션은 다시 풀로 반환되어 다음 요청에 재사용된다.

    ***

    ## 3. 기본 커넥션 풀 크기

    기본 풀 크기는 서버의 **물리 CPU 수에 비례**하여 자동으로 결정된다.
    공식 계산식은 다음과 같다:

    ```jsx
    기본 커넥션 수 = (물리 CPU 개수 × 2) + 1
    ```

    예를 들어,
    4코어 서버에서는 9개의 연결이,
    8코어 서버에서는 17개의 연결이 자동으로 생성된다.

    ***

    ## 4. 커넥션 풀 크기 설정

    개발자는 `connection_limit` 파라미터를 통해 직접 풀 크기를 지정할 수 있다.

    ```
    datasource db {
      provider = "postgresql"
      url      = "postgresql://user:password@localhost:5432/mydb?**connection_limit=5"**
    }

    ```

    위와 같이 작성하면, Prisma는 최대 5개의 DB 연결까지만 유지한다.
    필요에 따라 서버 성능이나 트래픽 양에 맞게 이 값을 조정할 수 있다.

    ***

    ## 5. 커넥션 풀 타임아웃

    Prisma는 기본적으로 **10초의 연결 대기 시간(pool_timeout)** 을 설정한다.
    이 시간 안에 커넥션을 확보하지 못하면 쿼리를 취소하고 에러를 발생시킨다.

    ```
    datasource db {
      provider = "postgresql"
      url      = "postgresql://user:password@localhost:5432/mydb?connection_limit=5&**pool_timeout=2"**
    }

    ```

    위 예시에서는 2초 동안 커넥션을 기다린 뒤,
    연결이 불가능할 경우 쿼리를 실패시킨다.
    만약 대량 데이터 삽입처럼 긴 작업을 수행해야 한다면
    `pool_timeout=0`으로 설정하여 타임아웃을 비활성화할 수도 있다.
    이 경우 큐에 쿼리가 무기한 대기하게 된다.

    ***

    ## 6. 자동 연결 제어

    Prisma는 엔진 내부에서 커넥션을 관리하기 때문에
    개발자가 직접 커넥션을 열거나 닫는 것은 불가능하다.
    즉, **모든 커넥션의 생성과 해제는 Prisma Query Engine이 자동으로 처리한다.**

    ***

    ## 결론

    > Prisma는 자체 Query Engine을 통해 커넥션 풀을 자동으로 관리한다.
    >
    > 커넥션 수는 CPU 기반으로 자동 계산되며,
    >
    > 설정값(`connection_limit`, `pool_timeout`)을 통해 세밀하게 조정할 수 있다.

  - ex. Prisma의 Migration 관리 방법
    참고: https://www.prisma.io/docs/orm/more/comparisons/prisma-and-drizzle#migrations

    ***

    ## Prisma의 Migration 관리 방식

    ## 1. 개요

    Prisma는 데이터베이스 구조 변경을 관리하기 위해
    **Prisma Migrate**라는 마이그레이션 도구를 사용한다.
    이 도구는 `.prisma` 스키마 파일을 기준으로
    DB 스키마를 자동으로 생성·변경·추적하도록 설계되어 있다.
    즉, 개발자가 스키마 파일만 수정하면
    Prisma가 자동으로 변경 사항을 감지하고
    적절한 SQL 마이그레이션 파일을 생성해준다.

    ***

    ## 2. 스키마 기반 관리 (Schema-First 구조)

    모든 데이터 모델은 `schema.prisma` 파일에 정의된다.
    개발자는 이 파일에서 모델, 필드, 관계 등을 선언하면 된다.
    예를 들어:

    ```
    model User {
      id    Int     @id @default(autoincrement())
      email String  @unique
      name  String?
    }
    ```

    이렇게 스키마를 작성하면 Prisma는 이를 읽어
    데이터베이스에 필요한 테이블 생성 SQL을 자동으로 생성한다. (수정시에도)

    ***

    ## 3. 마이그레이션 생성 과정

    스키마를 수정한 뒤, 다음 명령어를 실행하면 된다:

    ```bash
    npx prisma migrate dev --name add-user-table
    ```

    이 명령을 실행하면 Prisma가 다음을 수행한다:

    1. 스키마 변경 내용을 감지한다.
    2. 변경된 내용에 맞는 SQL 마이그레이션 파일을 생성한다.
    3. 개발용 데이터베이스(dev DB)에 변경 사항을 반영한다.
    4. 마이그레이션 이력을 `prisma/migrations` 폴더에 저장한다.
       > → 각 마이그레이션 폴더는 시점별 버전으로 관리되어,
       >
       > DB 변경 히스토리를 추적할 수 있게 된다.

    ***

    ## 4. 마이그레이션 적용 및 배포

    개발 환경과 배포 환경에서 각각 다음 명령을 사용한다.

    ### 개발 환경(dev)

    ```bash
    npx prisma migrate dev
    ```

    - 스키마 변경사항을 감지하고
      새 마이그레이션 파일을 생성한다.
    - 로컬 DB에 즉시 반영된다.
    - 스키마가 코드와 항상 동기화되도록 한다.

    ### 배포 환경(prod)

    ```bash
    npx prisma migrate deploy

    ```

    - 이미 생성된 마이그레이션 파일들을 실제 운영 DB에 순서대로 반영한다.
    - 코드 변경 없이 안정적으로 배포된다.

    ***

    ## 5. 마이그레이션 구조

    마이그레이션 파일은 다음과 같은 구조로 저장된다.

    ```
    prisma/
     └─ migrations/
         ├─ 20251103_add_user_table/
         │   ├─ migration.sql
         │   └─ steps.json
         └─ 20251105_add_post_table/
             ├─ migration.sql
             └─ steps.json

    ```

    - `migration.sql` : Prisma가 생성한 실제 SQL 명령문이 담긴 파일
    - `steps.json` : Prisma 내부에서 변경 단계를 추적하기 위한 메타데이터

    ***

    ## 6. 마이그레이션 재설정 및 초기화

    DB를 초기 상태로 되돌리고 싶을 때는 다음 명령을 사용한다:

    ```bash
    npx prisma migrate reset
    ```

    이 명령은 다음을 수행한다:

    1. 데이터베이스를 초기화한다.
    2. 기존 마이그레이션들을 순서대로 다시 실행한다.
    3. 초기 데이터(seed)가 있다면 자동으로 삽입된다.
       > 이 과정은 개발 환경에서만 권장되며,
       >
       > 운영 환경에서는 데이터를 보존해야 하므로 사용하지 않는다.

    ***

    ## 7. 스키마 변경 자동 반영 (db push)

    빠른 테스트나 임시 반영이 필요할 때는 다음 명령으로
    스키마를 DB에 직접 반영할 수 있다.

    ```bash
    npx prisma db push
    ```

    - SQL 파일을 생성하지 않고 바로 DB에 반영된다.
    - 실험적이거나 비영구적인 변경에 유용하다.
    - 단, 마이그레이션 기록이 남지 않기 때문에
      팀 프로젝트에서는 권장되지 않는다.

    ***

    ## 8. 롤백(Undo) 관리

    Prisma는 기본적으로 **이전 마이그레이션으로의 롤백 기능**을 제공하지 않는다.
    하지만 `git revert`로 스키마를 되돌리고
    새로 `migrate dev`를 실행하면
    자동으로 “되돌린 상태”의 마이그레이션이 생성된다.
    즉, Prisma는 “명시적 롤백” 대신
    “변경 이력을 코드로 관리하는 방식”을 채택하고 있다.

    ***

    ## 9. 협업 및 버전 관리

    - 모든 마이그레이션 파일은 **Git으로 버전 관리**된다.
    - 팀원 간 스키마 변경 충돌이 발생하면
      Prisma가 자동으로 감지하여 충돌을 표시해준다.
    - 각 팀원이 로컬에서 `migrate dev`를 실행하면
      동일한 DB 구조를 쉽게 재현할 수 있다.

    ***

- ORM(Prisma)을 사용하여 좋은 점과 나쁜 점
  출처: https://twentytwentyone.tistory.com/1234#google_vignette
  ***
  ## Prisma의 장단점을 정리해보았다.
  ### 장점
  | 항목                             | 설명                                                                                                                                                               |
  | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
  | **1. 완벽한 타입 안전성**        | Prisma는 TypeScript 전용 ORM으로, `.prisma` 스키마를 기반으로 DB 모델의 타입을 자동 생성함. 모든 쿼리와 결과가 컴파일 시점에서 검증되어 런타임 오류가 크게 줄어듦. |
  | **2. Schema-First 접근 방식**    | Prisma는 코드가 아닌 **스키마 파일(`schema.prisma`)을 단일**로 사용. 데이터 모델이 한눈에 보이고, 변경 관리가 용이함.                                              |
  | **3. Prisma Client 자동 생성**   | 스키마 변경 시 CLI 명령(`npx prisma generate`)만으로 **타입 안전한 쿼리 클라이언트**가 자동 생성됨. 별도의 Repository 코드가 필요 없음.                            |
  | **4. Prisma Studio 제공**        | 브라우저 기반 DB GUI 도구를 기본 내장. DB 데이터를 시각적으로 관리할 수 있어 개발 효율성이 높음.                                                                   |
  | \*\*5. 마이그레이션 자동화 및    |
  | 히스토리 관리\*\*                | `prisma migrate` 명령으로 스키마 변경 이력을 자동 추적하고, 버전별 마이그레이션을 자동 생성·적용 가능.                                                             |
  | **6. 강력한 개발자 경험 (DX)**   | 직관적인 API, 자동완성, 풍부한 에러 메시지, 세련된 CLI 등 **개발자 경험이 매우 뛰어남.** 문서 품질도 업계 최고 수준.                                               |
  | **7. 빠른 생산성과 협업 효율성** | 하나의 스키마 파일에서 데이터 모델이 정의되므로, 여러 개발자가 동시에 작업해도 충돌이 적고 코드 일관성이 유지됨.                                                   |
  ***
  ## 단점
  | 항목                                         | 설명                                                                                                                               |
  | -------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
  | **1. 복잡한 SQL 제어 한계**                  | Prisma는 추상화 수준이 높아 세밀한 SQL 제어나 복잡한 쿼리(서브쿼리, 윈도우 함수 등)를 직접 표현하기 어렵다. → Raw SQL로 보완 필요. |
  | **2. ORM 내부 구조의 블랙박스화**            | 자동 생성된 Prisma Client가 내부적으로 어떤 SQL을 실행하는지 완전히 직관적이지 않아, 디버깅이 어려울 수 있음.                      |
  | **3. 성능 오버헤드**                         | 내부적으로 쿼리를 파싱·매핑하는 과정이 추가되어, TypeORM이나 Knex.js보다 **쿼리 수행 속도가 약간 느림** (특히 복잡한 Join 시).     |
  | **4. 러닝 커브 (Prisma 전용 DSL 학습 필요)** | Prisma의 스키마 언어(`@id`, `@relation`, `@map` 등)는 일반 SQL이나 다른 ORM 문법과 다름. 초기에 새로 익혀야 함.                    |
  | **5. 의존성 잠금 (Lock-in)**                 | Prisma만의 구조(`schema.prisma`, Client generator 등`)는 다른 ORM으로의 전환이 매우 어렵다. → 기술적 종속성 높음.                  |
  | **6. 관계형 구조 중심 설계**                 | MongoDB 등 NoSQL을 지원하지만, Prisma의 설계 철학은 RDB에 최적화되어 있어 Document형 쿼리는 제한적.                                |
  | **7. 런타임 크기 증가**                      | Prisma Client가 대규모 프로젝트에서 상당히 큰 파일을 생성함 → 빌드 사이즈와 초기 로딩 시간이 늘어날 수 있음.                       |
  | **8. 특정 기능의 추상화 한계**               | 예를 들어, 부분 업데이트, 커스텀 집계(aggregation), DB 별 전용 함수 호출 등은 제한적이며 Raw Query로 처리해야 함.                  |
  | **9. 마이그레이션 충돌 가능성**              | 여러 개발자가 동시에 스키마를 변경하면 `migration lock` 이 발생할 수 있음. 협업 시 주의 필요.                                      |
  ***
  ## Prisma만의 특징 요약
  | 구분            | Prisma만의 강점                       | Prisma만의 단점                   |
  | --------------- | ------------------------------------- | --------------------------------- |
  | **핵심 철학**   | Schema-First, Type-Safe ORM           | 높은 추상화로 인한 제어력 부족    |
  | **생산성**      | 자동완성, Client 자동 생성, 빠른 개발 | 내부 구조가 블랙박스에 가까움     |
  | **성능**        | 단순 CRUD는 빠름                      | 복잡한 쿼리/Join 시 오버헤드 발생 |
  | **학습 난이도** | 문법 단순, DX 우수                    | Prisma DSL 별도 학습 필요         |
  | **확장성**      | 다양한 DB 지원, 프레임워크 통합 우수  | NoSQL/Raw SQL 제약 존재           |
  ***
  결론적으로
  Prisma는 타입 안정성과 개발 효율성에 특화된 차세대 TypeScript ORM으로,
  빠르고 안전한 개발에는 최적이지만, 복잡한 SQL 제어나 저수준 튜닝에는 한계가 있다.
  https://velog.io/@bard/3.-Prisma-Studio-%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EC%9E%90
  ⇒ 프리즈마 스튜디오 사용법!
- 다양한 ORM 라이브러리 살펴보기
  참고: https://notavoid.tistory.com/340

  ***

  ## 1. 현대 Node.js 개발과 ORM의 중요성

  ![image.png](attachment:1a8bbaba-5d05-44cd-bc9d-bf92f3fbea4f:image.png)
  2025년 현재, Node.js 생태계에서 TypeScript 기반의 서버 개발이 표준화되면서 ORM(Object-Relational Mapping) 도구의 선택은 프로젝트 구조와 유지보수 효율성을 결정하는 핵심 요인으로 자리잡았다.
  대표적인 ORM인 **Prisma**와 **TypeORM**은 모두 데이터베이스와의 상호작용을 단순화하여 생산성을 높이는 목적을 공유하지만, **접근 방식과 설계 철학에서 근본적인 차이**를 보인다.

  ***

  ## 2. Prisma의 특징과 장점

  ### Schema-First 접근 방식

  Prisma는 `.prisma` 파일에서 데이터 모델을 정의하고, 이를 기반으로 타입 안전한 클라이언트 코드를 자동 생성하는 **Schema-First** 철학을 채택하고 있다.

  ```
  model User {
    id        Int      @id @default(autoincrement())
    email     String   @unique
    name      String?
    posts     Post[]
    createdAt DateTime @default(now())
  }
  ```

  **Schema-First란** ORM이나 백엔드 설계에서 **“데이터 구조(스키마)”를 먼저 정의하고**, 그 스키마를 바탕으로 코드(모델, 타입, API 등)를 **자동 생성하는 방식**을 말한다. .primsa 파일에서 모델 먼저 쫙 써놓는걸 말함.

  ### 주요 장점

  - **완벽한 타입 안전성**: 컴파일 단계에서 모든 쿼리와 결과의 타입을 검증하며, IDE 자동완성과 오류 감지를 지원한다.
  - **직관적인 API 설계**: 간결하고 가독성 높은 메서드 기반 쿼리(`findUnique`, `findMany`, `include` 등)를 제공한다.
  - **자동 마이그레이션 시스템**: `prisma migrate` 명령을 통해 스키마 변경 사항을 안전하게 DB에 반영하고 롤백 및 버전 관리 기능을 내장한다.
  - **개발 도구 지원**: Prisma Studio를 통해 데이터베이스를 시각적으로 관리할 수 있으며, 개발 효율성을 높인다.

  ***

  ## 3. TypeORM의 특징과 장점

  ### Decorator 기반 Entity 정의

  TypeORM은 **Decorator 기반 Entity 정의 방식**을 사용하여, 클래스를 통해 데이터베이스 테이블을 선언적으로 매핑한다.

  ```tsx
  @Entity()
  export class User {
    @PrimaryGeneratedColumn()
    id: number;

    @Column({ unique: true })
    email: string;

    @Column({ nullable: true })
    name: string;
  }
  ```

  @ 를 이용해 직접 매핑한다.
  @Entity → 이건 엔티티다.
  @PrimaryGeneratedColumn → 이건 PK다
  **데이터베이스 구조를 코드 위에서 정의**하는 것이다.

  ### 주요 장점

  - **유연한 쿼리 작성**: Repository 패턴과 QueryBuilder를 통해 복잡한 SQL 쿼리를 명시적으로 구성할 수 있다.
  - **다양한 관계 설정 지원**: `@OneToOne`, `@OneToMany`, `@ManyToMany` 등 관계형 데이터베이스의 다양한 관계를 객체로 표현할 수 있다.
  - **패턴 선택 가능성**: Active Record 및 Data Mapper 패턴 중 프로젝트 성격에 맞는 구조를 선택할 수 있다.
  - **세밀한 제어력**: 트랜잭션, 캐싱, 커넥션 풀링 등 DB 연결을 세밀하게 제어할 수 있다.

  ***

  ## 4. 성능 비교

  | 항목               | Prisma    | TypeORM   | 비고                        |
  | ------------------ | --------- | --------- | --------------------------- |
  | 단순 CRUD 성능     | 매우 우수 | 우수      | Prisma의 최적화된 쿼리 생성 |
  | 복잡한 JOIN 쿼리   | 보통      | 매우 우수 | TypeORM의 QueryBuilder 강점 |
  | 대용량 데이터 처리 | 우수      | 보통      | Prisma의 메모리 효율성 우세 |
  | 초기 로딩 속도     | 보통      | 우수      | TypeORM의 경량 초기화 구조  |

  요약하면 Prisma는 경량·타입 안전성 중심, TypeORM은 고성능·세밀 제어 중심이다.

  ***

  ## 5. 실무 활용 비교

  | 활용 상황                      | 권장 ORM | 선택 이유                                                |
  | ------------------------------ | -------- | -------------------------------------------------------- |
  | **스타트업 / MVP 개발**        | Prisma   | 빠른 프로토타이핑, 자동 마이그레이션, 시각적 관리 도구   |
  | **대규모 엔터프라이즈 시스템** | TypeORM  | 복잡한 비즈니스 로직 및 트랜잭션 관리, 기존 DB 연동 유리 |

  ***

  ## 6. 데이터베이스 호환성

  | 데이터베이스 | Prisma | TypeORM | 비고                  |
  | ------------ | ------ | ------- | --------------------- |
  | PostgreSQL   | 지원   | 지원    | 완전 호환             |
  | MySQL        | 지원   | 지원    | 완전 호환             |
  | SQLite       | 지원   | 지원    | 테스트 환경 적합      |
  | MongoDB      | 지원   | 지원    | TypeORM은 별도 패키지 |
  | SQL Server   | 지원   | 지원    | 엔터프라이즈용        |
  | Oracle       | 미지원 | 지원    | TypeORM만 지원        |
  | CockroachDB  | 지원   | 미지원  | Prisma만 지원         |

  ***

  ## 7. 프로젝트 유형별 선택 기준

  ```jsx
  **Prisma를 선택해야 하는 경우
  1. **새로운 프로젝트** 시작
  2. **타입 안전성**이 최우선
  3. **빠른 프로토타이핑** 필요
  4. **팀의 TypeScript 숙련도**가 높음
  5. **자동 마이그레이션**이 중요**
  ```

  ```jsx
  **TypeORM을 선택해야 하는 경우
  1. **기존 데이터베이스** 활용
  2. **복잡한 쿼리** 최적화 필요
  3. **Java/C# 백그라운드** 개발자
  4. **세밀한 제어**가 중요
  5. **Oracle  [DB]** 사용 필요**
  ```

  ### 하이브리드 접근법

  실제 프로젝트에서는 **두 ORM을 조합**하여 사용하는 경우도 있다:

  - **주요 CRUD**: Prisma로 빠른 개발
  - **복잡한 분석 쿼리**: TypeORM QueryBuilder 활용
  - **레거시 연동**: TypeORM으로 기존 스키마 매핑

- 페이지네이션을 사용하는 다른 API 찾아보기

  - ex. https://docs.github.com/en/rest/using-the-rest-api/using-pagination-in-the-rest-api?apiVersion=2022-11-28

    ## Github REST API ?

    https://dietisdie.tistory.com/entry/GitHub-REST-API-%EC%82%AC%EC%9A%A9%EB%B2%95
    ⇒ Github의 작동방식인가? 뭔지 찾아봤는데, 깃헙 데이터를 가져올 수 있는 API라고함.
    GitHub의 REST API는 응답 데이터가 많을 경우,
    서버와 클라이언트의 부담을 줄이기 위해 **페이지 단위로 결과를 나누어 반환**한다.
    기본적으로 한 번의 요청에서 최대 **30개 항목**만 반환한다.

    ***

    ## 1.작동 방식 - Page-based Pagination

    페이지네이션이 적용된 응답에는 HTTP 응답 헤더(Header)에
    `Link` 필드가 포함되어 있으며,
    이 필드 안에는 다음과 같은 형태의 URL이 제공된다.

    ```
    link:
    <https://api.github.com/repositories/1300192/issues?page=2>; rel="next",
    <https://api.github.com/repositories/1300192/issues?page=515>; rel="last",
    <https://api.github.com/repositories/1300192/issues?page=1>; rel="first"

    ```

    - `rel="next"` → 다음 페이지의 URL
    - `rel="prev"` → 이전 페이지의 URL
    - `rel="first"` → 첫 번째 페이지의 URL
    - `rel="last"` → 마지막 페이지의 URL
      개발자는 이 링크를 이용해 다음 페이지로 요청을 반복하며 전체 데이터를 조회할 수 있다.

    ***

    ## 2. 페이지 크기 조정

    페이지당 반환되는 항목 수는 `per_page` 파라미터로 조정할 수 있다.

    ```bash
    GET /repos/octocat/Spoon-Knife/issues?per_page=100
    ```

    - 기본값: `30`
    - 최대값: `100`
      이 설정은 `Link` 헤더 내 URL에도 자동으로 반영된다.
      예:

    ```
    <https://api.github.com/issues?per_page=100&page=2>; rel="next"
    ```

    ***

    ## 3. 예시 코드 (JavaScript / Octokit.js)

    GitHub 공식 SDK인 **Octokit.js**를 사용하면 페이지네이션을 자동으로 처리할 수 있다.

    ```jsx
    import { Octokit } from "octokit";

    const octokit = new Octokit({});

    const issues = await octokit.paginate("GET /repos/{owner}/{repo}/issues", {
      owner: "octocat",
      repo: "Spoon-Knife",
      per_page: 100,
    });

    console.log(issues); // 모든 페이지의 결과가 하나의 배열로 반환됨
    ```

    `octokit.paginate()` 메서드는 내부적으로 `Link` 헤더를 파싱하여
    마지막 페이지까지 자동으로 순회(fetch)한 뒤, 모든 결과를 병합하여 반환한다.

    ***

    ### PS

    Page 기반 페이지네이션은 Offset 기반 페이지네이션이랑 같다.
    Offset ⇒ 40개 뒤에 20개 보내 달라
    Page ⇒ 3페이지를 줘라 ⇒ 내부에서는 Offset 기반처럼 돌아감

  - ex. https://developers.notion.com/reference/intro#pagination

    ## Notion API의 Pagination 정리

    Notion API는 한 번에 모든 데이터를 반환하지 않고,
    **Cursor 기반 페이지네이션**을 사용해 결과를 나누어 전송한다.
    즉, 이전 요청의 마지막 위치를 가리키는 커서를 기준으로 다음 데이터를 조회한다.
    이 방식은 GitHub과 달리 “page 번호”가 아니라,
    이전 응답이 어디까지 왔는지를 나타내는 cursor(문자열 토큰)를 사용한다.

    ***

    ## 1. 작동 방식

    응답에는 다음 두 개의 핵심 필드가 포함된다.
    | 필드 | 타입 | 설명 |
    | ------------- | ------- | --------------------------- |
    | `has_more` | boolean | 더 많은 결과가 있으면 true |
    | `next_cursor` | string | 다음 요청 시 사용할 커서 값 |
    `has_more`가 true인 경우, `next_cursor` 값을 다음 요청의 `start_cursor`로 전달하여 다음 데이터를 이어서 조회할 수 있다.

    ***

    ## 2. 요청 파라미터

    | 파라미터       | 타입   | 설명                                                      |
    | -------------- | ------ | --------------------------------------------------------- |
    | `page_size`    | number | 한 페이지당 반환할 항목 수 (기본 100, 최대 100)           |
    | `start_cursor` | string | 이전 응답의 `next_cursor` 값 (생략 시 첫 페이지부터 시작) |

    - GET 요청: 쿼리 스트링에 포함
    - POST 요청: 요청 본문(body)에 포함
      ⇒ GET이냐 POST이냐에 따라 파라미터가 어디에 포함되는지가 달라진다.

    ***

    ## 3. 예시 요청

    **< POST 요청의 경우 >**

    ```bash
    POST https://api.notion.com/v1/databases/<database_id>/query
    Content-Type: application/json
    {
      "start_cursor": "33e19cb9-751f-4993-b74d-234d67d0d534"
    }

    ```

    이전 응답의 `next_cursor`를 `start_cursor`로 전달하면
    다음 페이지 데이터를 반환한다.
    **< GET 요청의 경우 >**

    ```bash
    GET https://api.notion.com/v1/users?page_size=20&start_cursor=33e19cb9-751f-4993-b74d-234d67d0d534
    Authorization: Bearer <secret_token>
    Notion-Version: 2022-06-28
    ```

    이때 `start_cursor` 값은 이전 응답에서 받은 `next_cursor`를 그대로 사용한다.

    ***

    ## 4. 응답 구조 예시

    ```json
    {
      "object": "list",
      "results": [ ... ],
      "next_cursor": "33e19cb9-751f-4993-b74d-234d67d0d534",
      "has_more": true
    }
    ```

    ***

    ## 결론

    > Notion API는 Cursor 기반 페이지네이션을 사용하며,
    >
    > 응답의 `next_cursor`를 이용해 다음 데이터를 순차적으로 요청한다.
    >
    > `has_more`가 false가 될 때까지 반복 요청하여 전체 데이터를 조회한다.

  ## Page VS Cursor

  > Cursor 기반 페이지네이션은 데이터 추가·삭제·정렬 변경 등
  >
  > **데이터셋이 실시간으로 변하는 환경에서도 안정적으로 “이어서 조회”할 수 있는 방식**이다.
  >
  > 반면 Page(Offset) 기반은 “데이터의 상대적 위치”가 변하기 때문에,
  >
  > 일관성이 필요한 API(예: 실시간 피드, DB 조회 등)에서는 부적합하다.
  > 그럼 Offset 기반의 장점이 뭘까?
  > ⇒ 단순 + 직관적, 특정 페이지로 바로 넘어가기 좋다. 데이터가 정적이라면 이미 충분한 성능.
