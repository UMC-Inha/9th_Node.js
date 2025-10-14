- ECMAScript의 의미, 그리고 ES6 이후에는 각 버전에 따라 어떤 기능들이 새로 추가되었는지 찾고 정리해주세요
    
    ### 1. ECMAScript의 의미
    
    - Ecma International에서 ECMA-262 기술 규격에 따라 정의하고 있는 표준화된 스크립트 프로그래밍 언어 사양
    - 원래는 넷스케이프(Netscape)에서 개발한 JavaScript를 마이크로소프트(Microsoft)의 JScript 등과 같은 다른 구현체들과의 호환성 문제를 해결하고 언어 자체를 표준화 목적
    - JavaScript는 ECMAScript 사양을 준수하여 구현된 구현체 중 가장 잘 알려진 언어로,
    다른 브라우저 제조사나 환경(예: Node.js)은 이 ECMAScript 사양을 기반으로 자신들의 JavaScript 엔진을 구현함
    
    ### 2. ES6 이후
    
    - ES6
        - let, const, 화살표 함수, 클래스 문법, 모듈(import/export), 프로미스, 구조분해할당, 템플릿 리터럴, 스프레드 연산자 등 (위에서 정리한 내용)
    - ES7
        - Array.prototype.includes(), 거듭제곱 연산자
    - ES8
        - async/await 비동기 처리, Object.values(), Object.entries(), 문자열 패딩 등
    - ES9
        - Rest/Spread 속성, 비동기 이터레이터
    - ES10
        - Array.prototype.flat() / platMap(), Object.fromEntries()
    - ES11
        - Optional Chaining (?.)
        - 널 병합 연산자 (??): 왼족 피연산자가 null / undefined일 때 오른쪽 피연산자 반환, 그렇지 않으면 왼쪽 피연산자를 반환하는 논리 연산자
    - ES12
        - Promise.any()
        - 논리할당 연산자 &&=
    - ES13
        - Top-level await : 모듈의 최상위 레벨에서 await 사용 가능
    
- 워크북에서 소개한 프로젝트 아키텍처(Controller, Service, Data Access) 구조에 대해 더 정리하고, Data Access(DB) 레이어와의 결합도를 낮출 수 있는 구조를 고민해주세요.
    - Controller
        - 요청/응답 처리 역할
        - 클라이언트의 요청을 받아 service에 전달하고, service에서 처리된 결과를 받아 클라이언트에게 응답을 내려줌
    - Service
        - 비즈니스 로직 처리
        - 비즈니스 로직을 캡슐화 및 추상화. DB 접근이 필요한 경우 Data Access 레이어 이용
    - Data Access
        - 데이터베이스 상호작용을 위한 레이어
        - 쿼리를 수행하며 DB와 상호작용하는 곳
    
    DB 레이어와의 결합도를 낮춘다 = 비즈니스 로직이 특정 DB나 기술에 종속되지 않는다
    
    - 결합도가 높다 = 서비스 코드에서 직접 쿼리를 작성
    - 결합도를 낮추는 법 : 서비스 코드가 DB 구현 세부 사항에 직접 의존하지 않도록 하기
    
    방안
    
    1. DTO
        - DTO를 통해 계층간 데이터 전달 시 필요한 필드만 포함시킬 수 있어, DB 구조가 변경되어도 비즈니스 로직은 수정할 필요가 없음
        - DTO를 사용하면 비즈니스 로직은 DB의 엔티티 클래스(저수준)에 의존하는 대신 순수 자바 객체라는 추상화된 데이터 형태에 의존하게 됨
    2. Repository 패턴
        - 디비 접근을 추상화해서 인터페이스만 노출시키기
        - 즉, DB에 접근하는 코드를 한 곳에 모아두고, 나머지 코드에서는 그 저장소의 세부 구현을 몰라도 데이터를 다루 수 있게 하는 패턴
        - 비즈니스 로직은 DB가 어떻게 구현됐는지 몰라도 되기 때문에 결합도가 낮아짐
    3. DAO 패턴
        - 데이터베이스와 직접 상호작용하는 객체
        - Repository에 비해 추상화 수준이 낮고, 비즈니스 로직이 거의 없다.
        - DB 쿼리(CRUD)를 직접 캡슐화해서 제공하는 계층
    4. ORM 사용
        - SQL 대신 ORM 객체를 사용함으로써 DB 변경에도 서비스 코드에 미치는 영향을 최소화할 수 있다.
    
- 클린 아키텍처(Clean Architecture)와 의존성(Dependency)의 방향에 대해서도 찾아본 후 정리해주세요.
    
    ![image.png](attachment:0e63b967-a2b4-4be7-81b5-cc47f5ceb80d:image.png)
    
    모든 소스코드 의존성은 반드시 외부에서 내부로, 고수준 정책을 향해야 한다.
    
    즉, 비즈니스 로직을 담당하는 코드들이 DB 또는 Web같이 구체적인 세부 사항에 의존하지 않아야 한다. 이를 통해 비즈니스 로직(고수준 정책)은 세부 사항(저수준 정책)의 변경에 영향을 받지 않도록 할 수 있다.
    
    의존성 규칙을 지키기 위해서는 단순하고, 고립된 형태의 데이터 구조를 사용하는 것을 추천!
    
    - DB 형식의 데이터 구조나
    - Framework에 종속적인 데이터구조가 사용된다면(spring은 httpServletRequest, Django는 models.Model)
    
    → 이런 저수준 데이터 형식을 고수준에서 알아야 하기 때문에 의존성 규칙을 위반하게 된다.
    
    **DTO는 의존성 문제를 해결하는데 핵심적인 역할을 한다.**
    
    다만,
    
    DTO 사용 시 발생되는 몇몇 문제가 있다.
    
    ### **☑️ DTO간 중복 코드**
    
    요청은 CRUD로 이루어져있고, 요청에 따라 DTO를 어떻게 만들어야 할 까 고민을 많이 하게 된다. 프로젝트 초반에 로직 구현하다 보면, 생성/수정은 거의 비슷한 형식의 데이터와 검증 로직을 필요로 하게 된다. 이 때문에 통합할지 분리할지에 대한 고민을 많이 하게 된다.
    
    ex) 실제 제 코드입니다.
    
    ![image.png](attachment:50471b2a-189c-4cac-ada9-71dcc87d8872:image.png)
    
    ![image.png](attachment:691b4ccf-a659-4319-bbcd-402d01b99e5c:image.png)
    
    ![image.png](attachment:d4da1792-ee95-47c2-b192-1841cde6b3ba:image.png)
    
    createPubbleInput과 updatePubbleInput이 완전 똑같다.
    
    왜 이렇게 했는지 모르겠지만, 모종의 이유로 분리해놨었던거같은데, 지금은 잘 모르겠습니다 어쨌든!
    
    중복에도 종류가 있다고 한다.
    
    1. 진짜 중복 : 한 인스턴스가 변경되면, 동일한 변경을 그 인스턴스의 모든 복사본에 다시 적용해야한다.
    2. 우발적 중복 (거짓 중복) : 중복으로 보이는 두 코드의 영역이 각자의 경로로 발전한다면, 즉, 서로 다른 속도와 다른 이유로 변경되면 거짓 중복이다.
    
    지금 생각해보면 저때 개발 시 모든 데이터가 눈에 보여야 마음이 편해서 저렇게 작성했었던 것 같은데, 지금도 DTO는 생성/수정 용을 따로 만들 것 같긴 하다.
    
    (다만, 중복되는건 코드상으로는 PartialType같은 기능으로 코드를 줄일 것 같다.)
    
    **지금 정리하는 글의 출처인 우아한 기술블로그 글쓴이도 이렇게 분리하는 것을 선호한다고 한다.**
    
    ### **☑️ Entity가 DTO를 직접 참조하는 문제**
    
    나는 이런 적이 전혀 없긴 하지만, 엔티티에서 DTO 형식을 참조하는 경우도 당연히 의존성 규칙에 위반한다.
    
    ```jsx
    @Entity()
    export class Schedule {
      @PrimaryGeneratedColumn()
      id: number;
    
      @Column()
      title: string;
    
      @Column()
      startDate: Date;
    
      // DTO를 그대로 전달받아 사용하고있다!
      public static of(requestDto: CreateScheduleDto): Schedule {
        const entity = new Schedule();
        entity.title = requestDto.title;
        entity.startDate = requestDto.startDate;
        return entity;
      }
    }
    ```
    
    이렇게 하는것보다 service 계층에서 dto의 내부 원소들을 풀어 그 값들을 전달해줘야한다.
    
    ### **☑️ Crossing Boundaries**
    
    고수준 정책은 저수준 정책에 의존해서는 안된다.
    
    예를 들어 일반적인 조회 요청 시 제어흐름과 소스코드 의존성은 아래와 같다.
    
    ![image.png](attachment:2a7ffa27-a8df-453f-a679-a1e8444e0ad8:image.png)
    
    이렇게 제어 흐름과 의존성을 갖게 된다면 service가 구체적인 RDBMS를 구현한 구현체(저수준 정책)를 직접 참조하게 되어, 이는 의존성 규칙을 위반한다.
    
    **이를 해결하기 위해 의존성 역전 원칙을 사용한다**
    
    ![image.png](attachment:21d1eac9-ab9b-4546-b681-c334feb72b01:image.png)
    
    - DB 작업을 수행하기 위한 인터페이스, 예를 들어 save(), findById() 등을 정의한다. 이는 비즈니스 로직 관점에서 정의된다.
    - 의존성 역전을 적용하기 전에는 service 의존성이 아키텍처의 바깥쪽(저수준)을 향하지만, 의존성 역전을 통해 추상호된 repository 인터페이스를 향하게 된다. 이 Repository 인터페이스는 service와 같은 고수준 계층에 정의된다.
    
    배민에서, 배달과 배달료의 관계를 보자.
    
    ![image.png](attachment:17533ade-5295-4617-8bc4-8441228267b2:image.png)
    
    배달료를 구할 때 크게 거리 기반과 구역 할증 기반 추가 요금과 같이 2가지 정책이 있다. 
    
    ![image.png](attachment:c3189cc8-be76-4015-905d-ae5c27c66472:image.png)
    
    하지만 이 제어 흐름데로 소스코드 의존성을 준다면, 배달에 관한 정책이 구체적인 거리 배달료/구역배달료 정책에 강하게 의존하게되어, 각 정책이 변경될 때마다 배달에 대한 부분도 변경될 여지가 생긴다.
    
    여기서 DIP를 적용하여, 구체적인 배달료를 구하는 정책을 분리시킬 수 있다.
    
    ![image.png](attachment:4ef62443-8bb4-48d5-ac4f-bb0f5494d108:image.png)
    
    여기서 한번 더 나아가, 클라이언트에서 배달료를 구하기 위해 내부 구현을 모르는 것이 좋기 때문에 두 인터페이스를 합친 Facade 인터페이스 패턴을 적용했다고 한다. (아래와 같이)
    
    ![image.png](attachment:46c1002b-4c8c-47a7-84ad-4e75b926a67f:image.png)
    
    ### ⚠️ 인터페이스 선언 시 주의점
    
    - 클라이언트가 필요로 하는 메서드를 기반으로 분리되어야 한다.
        - 클라이언트가 필요하지 않는 메서드까지 노출하지 않도록!
        
        ```jsx
        interface ExtraService {
            calculateExtraDeliveryFee(...);
            calculateExtraDeliveryTip(...);
            calculateExtraDeliveryTime(...);
        }
        ```
        
        위처럼 특정 객체가 배달료, 팁, 배달시간의 할증에 관한 정보를 가지고 있는데, 각 메소드를 소비하는 클라이언트들이 서로다른 메소드를 소비한다면, 서로 받는 영향을 줄이기 위해 각 클라이언트 기반으로 인터페이스를 분리하는 것이 좋다.
        
    
    ### Reference
    
     https://techblog.woowahan.com/2647/