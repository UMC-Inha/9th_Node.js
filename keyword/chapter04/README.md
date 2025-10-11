- ES
  출처: https://ko.wikipedia.org/wiki/ECMA%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8 / https://velog.io/@janghoon333/study-ES
  ***
  **ES**란 ECMA 스크립트로, 쉽게 말해 **'Javascript의 표준 규격’**이다.
  JavaScript는 1995년도 “넷스케이프”에서 시작된 언어 **⇒** 자바스크립트의 인기에 편승하기 위해, 마이크로소프트에서 비스무리한 JScript를 만듦. ⇒ “브라우저 전쟁”의 시작!
  ⇒ JScript와 JavaScript가 문법이랑 동작방식이 조금씩 달랐음 ⇒ 근데 서로 경쟁하면서 점점 서로 달라져서 개발자들이 불편 ⇒ “표준화의 필요성 대두.”
  ⇒ 이 문제를 해결하기 위해 **ECMA(European Computer Manufacturers Association)**, 현재의 **ECMA International** 이 자바스크립트의 명세를 표준화하기로 함. ⇒ 그 결과 만들어진 문서가 바로 **ECMA-262**, 즉 **ECMAScript(ES)**이다. (당시 1997년도)
  ⇒ 이후 웹 브라우저들이 이 표준을 따르기 시작하면서 웹 생태계가 **통일**됐다.
  **ETC)** 262이란 숫자가 붙은 이유는 ⇒ ECMA의 262번째 문서라는 뜻 ⇒ 이 262번째 문서가 자바스크립트의 표준화에 관한 문서라고 함. 번외로 ECMA-404는 JSON 표준화에 관한 문서.
- ES6
  ECMAScript 2015로도 알려져 있는 ECMAScript 6**(ES6)**는 ECMAScript 표준의 가장 최신 버전이다.
  자바스크립트의 두번째 주요 개정판으로, **현대 자바스크립트의 시작점**이라고 불릴 만큼 큰 변화를 가져왔다고 한다.(첫번째 주요 개정판인 ES3는 1999년도에 나왔다고 한다)
  - ES6의 주요 변화 및 특징
    출처: https://velog.io/@kim_unknown_/JavaScript-ES6 / https://velog.io/@jinyongp/Interview-JavaScript-ES6%EC%9D%98-%EC%A3%BC%EC%9A%94-%EB%B3%80%ED%99%94%EC%A0%90
    ***
    크게 보면 **10개** 정도의 주요변화가 생겼다.
    - **`let`, `const` — 새로운 변수 선언 방식 추가**
      ES5까지는 변수 선언 방식이 var만 있었다고 한다.
      ⇒ `var`은 재정의와 재선언 모두 가능하다.
      - `var`의 문제점
      - ① 변수 선언이 유연하기 때문에 예기치 못한 값을 반환할 수 있다.
      - ② 코드가 길어진다면 어디에서 어떻게 사용 될지 파악하기 힘들다.
      - ③ 함수 레벨 스코프로 인해 함수 외부에서 선언한 변수는 모두 전역 변수로 된다.
      - ④ 변수 선언문 이전에 변수를 참조하면 언제나 undefined를 반환한다. (호이스팅 발생)
      **`let`** → 재할당 가능, 블록 스코프 / **`const`** → 재할당 불가, 블록 스코프
      ⇒ 코드의 예측 가능성과 안정성 대폭 향상
      ⇒ 따라서, var은 잘 사용하지 않으며, let과 const 키워드를 사용하는 것을 권장한다.
    - **화살표 함수 (Arrow Function) 추가**
      화살표 함수는 함수 표현식을 보다 단순하고 간결한 작성하는 문법이다.
      추가로, 화살표 함수의 `this`가 **상위 스코프**로 고정된다.
      ```jsx
      // 기존 함수
      function Person() {
        this.age = 0;
        setInterval(function () {
          this.age++; // this는 global(window)
          // 스코프를 Person으로 하고싶으면.. Person()선언할때 self = this로 저장해두고
          // =>self.age 로 해야함.
        }, 1000);
      }

      // 화살표 함수
      function Person() {
        this.age = 0;
        setInterval(() => {
          this.age++; // 상위 스코프(this = Person)
        }, 1000);
      }
      ```
      ⇒ 훨씬 더 간결하고 가독성도 좋고 깔끔해보인다.
    - **클래스(Class) 문법 정비**
      프로토타입이던 ES5의 클래스 문법을 개편.
      추가로 **`extends`** 의 등장으로 **상속**도 간편해짐.
      ```jsx
      class Admin extends User {
        // extends로 상속 받음
        constructor(name, role) {
          super(name); // 부모(User)의 생성자 호출
          this.role = role;
        }
        sayHi() {
          super.sayHi(); // 부모의 sayHi() 메서드 실행
          console.log(`그리고 나는 ${this.role}이야!`);
        }
      }
      ```
      코드 재사용, 구조적 설계, 객체지향적 계층 구현이 가능해졌다.
    - **공식 모듈 시스템 (ES Module의 도입)**
      초기에는 JS 내의 모든 코드는 전역 스코프를 가졌었다. ⇒ 코드의 재사용성과 충돌 문제가 많이 생김 ⇒ 추후에는 모듈이라는 개념이 생겼고 CommonJS 방식 같은 여러 비표준 모듈 시스템을 썼다. require() 이거. ⇒ 혼란스러움
      ES6 부터는 자바스크립트 자체에 모듈 기능을 공식적으로 표준화해서 내장했다.
      ⇒ 이게 ESM. import export 우리가 많이 쓰는 이것! ⇒ 훨씬 깔끔하고 안전하게 모듈을 내보내고 가져올수있게 됨 ⇒ 코드의 재사용 / 분리 / 의존성 관리가 가능해짐.
      ```jsx
      // math.js export
      export const add = (a, b) => a + b;

      // main.js import
      import { add } from "./math.js";
      console.log(add(2, 3)); // 5
      ```
    - **템플릿 리터럴 (Template Literal) 추가**
      백틱(```)을 이용해 문자열 내 변수와 줄바꿈을 자연스럽게 지원.
      ```jsx
      const name = "Jin";
      console.log(`Hello, ${name}!
      오늘은 ${new Date().toLocaleDateString()} 입니다.`);
      ```
      ⇒ 가독성이 좋아졌다. + 함수도 파싱 가능하다.
    - **구조분해할당(destructing)**
      배열과 객체의 구조를 한번에 변수로 분해하는 문법 기능 추가. ⇒ 더 쉽게 할당 가능해졌다.
      ```jsx
      const [a, b] = [1, 2];
      const { name, age } = { name: "Jin", age: 25 };
      ```
    - **Rest 매개변수와 Spread 연산자 도입 `...`**
      매우 간편한 문법!
      - **Rest Parameter** => 함수에 전달된 모든 인자를 하나의 배열로 받아줌.
      - **Spread Operator** ⇒ **배열(또는 객체)의 요소를 풀어서(spread)** 복사하거나 합칠 때 사용. ⇒ 배열 요소를 하나씩 꺼내서 복사한다.
      ```jsx
      function sum(...nums) {
        // Rest Parameter
        return nums.reduce((a, b) => a + b);
      }

      const arr = [1, 2, 3];
      const copy = [...arr]; // Spread Operator
      ```
      **`...` 함수 정의 안에 있으면 → 나머지 매개변수(rest)**
      **`...`** **함수 호출이나 배열/객체 안에 있으면 → 전개 연산자(spread)**
    - **기본 매개변수 (Default Parameter) 도입**
      ES5 시절에는 이렇게 기본값을 할당했다고 한다.
      ```jsx
      function greetOld(name) {
        name = name || "Guest"; // '', 0, false도 "Guest"로 바뀜
      }
      ```
      ES6 와서야 이게 가능해졌다고한다.
      ```jsx
      function greet(name = "Guest") {
        //기본값 Guest로 설정
        console.log(`Hello, ${name}`);
      }

      greet(); // Hello, Guest   (인자 없음 → 기본값(Guest 사용)
      ```
    - **Promise 객체 (새로운 비동기 코드 작성 방법)**
      1. ES6 이전에는 콜백 방식으로 비동기를 처리했다.
      ```jsx
      getData(function (result) {
        process(result, function (processed) {
          save(processed, function (saved) {
            console.log("완료!");
          });
        });
      });
      ```
      ⇒ 이런식으로 함수안에 함수를 담는 방식… ⇒ 중첩 깊어지면 콜백 지옥 발생 ⇒ 가독성 망함 try catch 예외처리도 불가능
      1. ES6 부터는 “Promise”가 도입됨. ⇒ Promise는 **비동기 로직을 ‘값’처럼 다루게 해주는 객체.**
      이런식으로 프로미스 객체를 만듦.
      ```jsx
      function eat() {
        return new Promise((resolve, reject) => {
          // 프로미스 객체 만듦
          console.log("밥 먹는 중...");
          setTimeout(() => {
            resolve("밥 다 먹음!"); // 성공 시 실행
            reject("밥 먹다 체함"); // 실패 시 이렇게
          }, 1000);
        });
      }
      ```
      이런식으로 프로미스 객체를 여러개 만들고
      ```jsx
      eat()
        .then(() => wash())
        .then(() => watchTV())
        .then(() => console.log("끝!"))
        .catch((err) => console.error("문제 생김!", err));
      ```
      이렇게 프로미스 객체를 연결(체이닝) 하는 방식. ⇒ 깔끔하게 순차 실행이 가능해짐.
      1. async/await 로 진화
      우리가 유용하게쓰는 async 얘네가 **Promise를 기반으로 만들어진 문법**. 즉, Promise를 더 쉽고 가독성 좋게 쓰기 위한 진화버전.
      - async ⇒ 이 함수는 비동기로 동작하고, **Promise를 반환하는 함수 ⇒** Promise 객체를 더 쉽게 선언하기 위한 문법.
      ```jsx
      // async 함수 선언 => 리턴타입은 프로미스
      async function hello() {
        return "안녕!";
      }
      ```
      - await ⇒ **Promise가 끝날 때까지 기다렸다가** 결과를 받아오는 키워드. (무조건 async 안에서만 사용 가능)
      ```jsx
      async function daily() {
        try {
          const eatResult = await eat(); // 밥 먹기 기다림
          console.log(eatResult);

          const washResult = await wash(); // 설거지 기다림
          console.log(washResult);

          const tvResult = await watchTV(); // TV 보기 기다림
          console.log(tvResult);

          console.log("하루 일정 끝!");
        } catch (err) {
          console.error("문제 발생!", err);
        }
      }

      daily();
      ```
      즉 `await`는 Promise 체이닝(`.then()`)을 더 쉽고 보기 좋게 쓰기 위한 문법.
    - **Map / Set (새 자료구조) 도입**
      1. Map ⇒ 딕셔너리 비슷하게 키와 벨류로 관리하는 자료구조
      ```jsx
      const map = new Map();

      // 값 넣기
      map.set("name", "진"); // 키: 벨류
      map.set(1, "숫자 키도 가능");
      map.set({ id: 1 }, "객체 키도 가능");

      // 값 꺼내기
      console.log(map.get("name")); // 진
      console.log(map.get(1)); // 숫자 키도 가능
      ```
      1. Set ⇒ 중복 없는 배열
      ```jsx
      const set = new Set([1, 2, 2, 3, 3, 3]);
      console.log(set); // Set {1, 2, 3}

      // 값 추가
      set.add(4);
      set.add(4); // 중복 무시됨

      // 확인
      console.log(set.has(2)); // true

      // 삭제
      set.delete(1);

      // 크기
      console.log(set.size); // 3
      ```
      ⇒ 기존 객체와 배열의 빈틈을 보완하는 유연한 자료구조들을 도입했다! ⇒ 데이터 다루기 편해짐.
  - **ES6**를 중요시 하는 이유
    사실 좀 충격을 받았다. ⇒ 내가 알고있는 자바스크립트의 특징들과 문법이 2015년에서야 만들어진것들이라니… 이 전에는 어떻게 코딩을 하고있던거지..?
    ***
    ES6가 중요시되는 이유는 자바스크립트의 현대화 시작점이기 때문이다.
    - ES6은 JS를 “웹 전용 스크립트”에서 “정식 프로그래밍 언어”로 발전시킴.
    - 변수 안정성 확보, 비동기 로직 개선, 클래스, 모듈 시스템 ⇒ 코드 구조화, 유지보수, 대형 프로젝트가 가능해짐.
    - 새로운 문법으로 생산성·가독성 향상 ⇒ 최신 프레임워크의 기반이 되었다.
- ES Module
  출처: https://velog.io/@khy226/CommonJS-vs.-ES-Module
  ***
  초창기 JS의 파일은 모두 전역스코프를 가졌었음 ⇒ 너무 불안정함 ⇒ 파일간 코드 재사용을 위해 모듈 시스템을 도입함.
  - **CommonJS(CJS)** — Node.js 초창기부터 사용된 방식 (비 표준)
  - **ES Modules(ESM)** — ECMAScript(ES6, 2015) 표준으로 도입된 공식 방식
  ***
  1. **Common JS**

     - Node.js 기본 모듈 시스템 (설정 없이 기본값)
     - **`require()`** 로 모듈을 불러오고, **`module.exports`** 로 내보냄
     - **동기적(런타임)** 으로 동작 — 실행 시점에 모듈 로딩 ⇒ 실행전까진 로딩 X
     - **브라우저에서는 동작하지 않음**

     ```jsx
     // 모듈 내보내기
     module.exports.add = (a, b) => a + b;

     // 모듈 불러오기
     const math = require("./math");
     ```

     - `require()`가 실행되면 해당 파일이 **즉시 실행**되고 반환값을 `module.exports` 객체에 담아서 반환함.
     - 따라서 **동기적(Synchronous)** 으로 작동. ⇒ Promise나 `await`을 직접 쓸 수 없음.

     **📌 top-level await X**, 즉 파일 최상단에서 `await` 사용 불가능.
  ***
  1. **ES Modules**

     - **ES6 (2015)** 부터 표준화된 공식 모듈 시스템
     - **`import` / `export`** 문법 사용
     - **정적 로딩 (compile-time)** — 실행 전 미리 파싱
     - **top-level await 가능**
     - 브라우저 + Node.js 모두 사용 가능
     - package.json ⇒ “type”: “module” 이거 명시 해야함.

     ```jsx
     export const add = (x, y) => x + y; // 모듈 내보내기

     import add from "./calculator.js"; // 모듈 불러오기
     ```

     - **실행 전** 모든 `import` / `export` 구문을 **미리 파싱**함.
     - 덕분에 코드 실행 전에 의존성을 완전히 분석 가능.
     - 모듈을 **병렬로 다운로드**, **순차적으로 실행**.
     - top-level await 지원 → 비동기 초기화 가능.
  ***
  1. **Top-level Await가 무엇일까?**

     기존에는 await는 async 함수 안에서만 쓸수있었음.

     ```jsx
     async function run() {
       const res = await fetch("https://api.example.com/data");
       const data = await res.json();
       console.log(data);
     }
     run(); // OK
     ```

     ⇒ 근데 ESM 쓰면 모듈은 기본적으로 비동기적으로 로딩되게 바뀜 !!!!

     ```jsx
     // top-level await (ES Module 환경)
     const res = await fetch("https://jsonplaceholder.typicode.com/todos/1");
     const data = await res.json();

     console.log("결과:", data);
     ```

     그래서 이렇게 바로 모듈을 await으로 불러올수있게됨 ⇒ 이게 top-level await. (CommonJS에서는 문법 에러로 뜸)
- 프로젝트 아키텍처
  - 프로젝트 아키텍처가 중요한 이유
    출처: https://brunch.co.kr/@theopenproduct/38
    ***
    프로젝트 아키텍처(Project Architecture)는
    소프트웨어 시스템을 구성하는 **요소 간의 구조적 관계와 설계 원칙**을 정의하는 체계이다.
    즉, 코드와 기능을 어떤 방식으로 분리하고, 상호작용하게 할지를 결정하는 **시스템의 뼈대**다.
    아키텍처는 단순한 디렉터리 구조 이상의 의미를 가지며,
    개발 효율성·확장성·유지보수성·품질관리 등 소프트웨어 전반의 안정성과 직결된다.
    ![“좋은 설계(good design)”와 “무설계(no design)”의 장기 생산성 차이](attachment:3b53e10a-65ec-4bb7-81e0-832a25a655fc:스크린샷_2025-10-11_오후_3.59.30.png)
    “좋은 설계(good design)”와 “무설계(no design)”의 장기 생산성 차이
    유지보수성 / 확장성 / 협업 효율성 / 재사용성 / 품질 보장 / 자동화 및 운영 효율성
    ⇒ 즉, 시스템의 수명, 품질, 협업 효율을 결정짓는 핵심 요소들과 직결된다.
    ⇒ 체계적으로 구조(아키텍처)가 초기에 잘잡혀있어야 이것들이 보장됨.
    ⇒ 초반에 체계가 제대로 안잡혀있으면 나중에 바꾸고 수정하고 이러면서 더 지출이 커짐.
  - Service-Oriented Architecture(Service Layer Pattern)
    출처: https://azderica.github.io/01-architecture-soa/
    ***
    ![image.png](attachment:c2afd14b-91e1-4f67-90af-a5b3a12c5c7c:image.png)
    모놀리식 구조와 MSA의 중간 지점 정도라고 보면 된다. → 기능은 분리하되, 연결은 중앙에서 통합한다.
    | 구조           | 특징 한 줄 요약                                          |
    | -------------- | -------------------------------------------------------- |
    | **Monolithic** | 모든 기능이 한 덩어리 안에 다 들어있음                   |
    | **SOA**        | 기능별로 “서비스 단위”로 나누고, 중앙 허브를 통해 연결됨 |
    | **MSA**        | 각 서비스가 완전히 독립되어, 서로 직접 통신함            |
    ***
    SOA는 여러 독립된 서비스 프로젝트들을 중앙 허브가 연결해 하나의 유기적인 시스템으로 만드는 구조다.
    - 서비스는 독립적으로 존재하고,
    - 중앙 허브가 모든 통신과 데이터 흐름을 제어하며,
    - 덕분에 시스템 전체가 유연하고 확장 가능해진다.
    **SOA의 철학**
    | 이유            | 설명                                     |
    | --------------- | ---------------------------------------- |
    | **유연성**      | 한 서비스 수정해도 다른 서비스 영향 적음 |
    | **확장성**      | AI나 채팅 기능만 별도로 서버 확장 가능   |
    | **협업 용이**   | 팀별로 다른 서비스 독립 개발 가능        |
    | **테스트 용이** | 각 기능 단위로 테스트 가능               |
    | **배포 효율**   | 수정된 서비스만 다시 배포하면 됨         |
    ***
    ![스크린샷 2025-10-11 오후 4.25.05.png](attachment:d089fd88-5df1-40cc-9686-9b35509c5a09:스크린샷_2025-10-11_오후_4.25.05.png)
    이런 느낌으로 연결되어있다.
    ```jsx
    [Frontend]  ←→  [BE-core-service]  ←→  [BE-chat-service]
                                 ↘
                                  ↘
                                  [BE-ai-service]
    ```
    ⇒ 전에 했던 프젝인데 SOA 형식으로 아키텍처를 구상해서 했었다. ⇒ Core 백엔드가 중심에 있고 Chat과 AI 서버와 Rest API 형식으로 통신하면서 서로를 연결함.
  - MVC 패턴
    출처: https://ko.wikipedia.org/wiki/%EB%AA%A8%EB%8D%B8-%EB%B7%B0-%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC
    ***
    **MVC(Model–View–Controller)** 패턴은
    소프트웨어 설계에서 **사용자 인터페이스(UI)와 비즈니스 로직, 데이터 처리 로직을 분리**하는 대표적인 아키텍처 패턴이다.
    ⇒ UI와 비즈니스 로직의 분리를 통해 유지보수성과 확장성을 높이고, 코드 간 결합도를 낮춘다.
    즉, **“화면을 바꿔도 로직이 깨지지 않고, 로직을 바꿔도 화면이 그대로 유지되게 하는 구조”** 를 만드는 것이 MVC의 핵심이다.
    ![image.png](attachment:112fea13-1046-4ea6-a236-512a6f3df0e5:image.png)
    > **Model**은 데이터와 비즈니스 로직, **View**는 UI, **Controller**는 입력처리와 모델과 뷰의 연결을 담당한다.
    >
    > 이 구조는 오늘날 거의 모든 웹·모바일 프레임워크의 기본 설계 원칙이다.
  - 그 외 다른 프로젝트 구조
    ### DDD (도메인 주도 설계)
    ***
    ```jsx
    //기술 중심(계층형 구조)
    controller/
    service/
    repository/
    ```
    **무엇을 하는 코드인지(Controller/Service)**로 폴더를 나눔. ⇒ MVC
    ```jsx
    // 업무 중심(비즈니스 중심) 구조
    order/      ← 주문 도메인
    user/       ← 회원 도메인
    inventory/  ← 재고 도메인
    ```
    **이 코드가 어떤 업무를 담당하는지(주문/회원/재고)**로 나눔. ⇒ 이게 DDD
    DDD는 오른쪽 ⇒ 즉, 코드를 **기능**기준이 아니라, **업무** 기준으로 나누는 설계 방식.
    > 한 업무(도메인) 안에서 필요한 기능을 다 모아두는 방식.
    >
    > (Controller, Service, Repository가 전부 각각 도메인 마다 안에 들어있음)
    | 장점               | 설명                                                         |
    | ------------------ | ------------------------------------------------------------ |
    | 비즈니스 중심 설계 | 코드 구조가 실제 도메인(업무) 언어와 일치하여 이해하기 쉽다. |
    | 유지보수 용이      | 도메인별로 코드가 독립되어 수정이나 추가가 간단하다.         |
    | 확장성 높음        | 도메인 경계가 명확해 새로운 기능 추가나 MSA 전환이 용이하다. |
    | 협업 효율 향상     | 여러 개발자가 서로 다른 도메인을 병렬로 개발할 수 있다.      |
    | 단점                       | 설명                                                              |
    | -------------------------- | ----------------------------------------------------------------- |
    | 초기 설계 복잡             | 도메인 경계를 정하고 계층을 나누는 과정이 어렵다.                 |
    | 작은 프로젝트에는 비효율적 | 단순 CRUD 프로젝트에서는 구조가 과도하게 복잡해진다.              |
    | 팀 전체의 이해 필요        | 일부 개발자만 개념을 이해하면 구조가 쉽게 무너질 수 있다.         |
    | 구현 난이도 높음           | 엔티티, 애그리게이트, 도메인 이벤트 등 개념이 많고 설계가 어렵다. |
    DDD는 복잡한 비즈니스 로직이 있는 중대형 프로젝트에서 유지보수성과 확장성 면에서 강력한 장점을 가지지만, 작은 규모의 서비스에는 불필요하게 복잡해질 수 있다.
    ***
    ![스크린샷 2025-10-11 오후 4.53.24.png](attachment:4dd30078-31c6-4404-8b39-875e25febf19:스크린샷_2025-10-11_오후_4.53.24.png)
    장고에서는 DDD로 프로젝트를 만들었다. (완벽한 DDD는 아니고 야매 DDD인것 같다)
    Django는 기본적으로 앱 구조가 도메인 단위로 나뉘어 있기 때문에 DDD를 적용하기 좋은 프레임워크라고 한다. 처음 백엔드 입문을 장고로 해서 그런지 이게 DDD 패턴이라는 이름이 있다는걸 이번 워크북 하면서 처음 깨달았다.
- 비즈니스 로직
  출처: https://velog.io/@eddy_song/domain-logic
  ***
  **비즈니스 로직**이란…
  “프로그램이 단순히 데이터를 보여주는 것을 넘어서, **업무 규칙에 따라 데이터를 처리하고 결정하는 로직**”
  즉, “이 서비스가 실제로 어떤 일을 어떻게 처리해야 하는지 정의한 핵심 규칙”
  예시를 보는게 더 와닿을것 같다. 이런 “규칙, 계산, 조건 판단, 데이터 조작”이 바로 비즈니스 로직이다.
  | 상황               | 비즈니스 로직 예시                                             |
  | ------------------ | -------------------------------------------------------------- |
  | 회원가입           | 이메일 중복 검증 → 비밀번호 암호화 → DB 저장                   |
  | 포인트 적립 서비스 | 결제 금액의 5%를 포인트로 계산해 사용자 계정에 더하기          |
  | 플리마켓 앱        | 출석체크 시 하루 1회만 포인트 지급, 이미 지급된 날엔 예외 처리 |
  | 창고 관리 시스템   | 재고 수량이 0 이하일 때 출고 요청 거부                         |
  ***
  근데 비즈니스 로직 외에 **서비스 로직**도 있다.
  - 그 코드가 **‘현실 세계의 규칙’을 다루고 있다면 → 비즈니스(도메인) 로직**
  - 단순히 **입력·출력·저장·전달만 하고 있다면 → 서비스 로직(UI, DB, 네트워크)**
  | 구분 기준 | 비즈니스(도메인) 로직                                              | 서비스 로직                                                   |
  | --------- | ------------------------------------------------------------------ | ------------------------------------------------------------- |
  | 하는 일   | 현실 문제에 대한 판단, 규칙, 정책 처리                             | 데이터를 전달하고 표시하는 역할                               |
  | 핵심 질문 | “이게 어떻게 동작해야 하지?”                                       | “이걸 어디서 가져오고, 어떻게 보여줄까?”                      |
  | 예시      | 계좌 잔액이 충분한지 확인- 송금 수수료 계산- 포인트 적립 규칙 적용 | DB에서 잔액 조회- API 호출로 외부 결제 요청- 결과를 UI에 표시 |
  | 의존성    | 외부 기술(X) — 순수한 규칙                                         | 외부 기술(O) — DB, 네트워크, 프론트엔드 등                    |
  | 목적      | 비즈니스 의사결정                                                  | 기술적 전달 및 실행                                           |
  - **결정 주체는 도메인**
  - **흐름 주체는 서비스**
  ```jsx
  [요청]
   ↓
  Controller (API 입구)
   ↓
  Service (흐름 제어)
   ↓
  Domain (비즈니스 규칙 실행)
   ↓
  Repository (데이터 저장)
   ↓
  [응답]
  ```
  ```jsx
  [응답]
     ↑
  Controller (API 출구)
     ↑
  Service (결과 조합)
     ↑
  Domain (비즈니스 결과 반환)
     ↑
  Repository (데이터 조회)
     ↑
  [요청]
  ```
  “**실제 실행**은 도메인에서 일어나지만, **흐름의 조립과 명령**은 서비스에서 한다.”
  ⇒ 왜 이렇게까지 구분을 하는지 궁금했는데 ⇒ **명확한 관심사의 분리를 목적으로 나눈다고 한다 ⇒** 이해도 향상과 변경 용이성을 얻게 되고 클린 아키텍처에 가까워진다고 함.
- DTO
  DTO(Data Transfer Object) ⇒ 데이터 전달용 객체
  즉, 계층 간 데이터를 옮기기 위한 전용 객체를 뜻한다.
  **쓰는 이유**
  | 이유            | 설명                                                            |
  | --------------- | --------------------------------------------------------------- |
  | **보안**        | Entity(DB 모델)를 그대로 노출하면 안 됨 (비밀번호 등 포함 가능) |
  | **유연성**      | 요청/응답 형식을 API 요구에 맞게 변환 가능                      |
  | **의존성 분리** | Service, Controller가 Domain(Entity)에 직접 의존하지 않게 함    |
  | **성능**        | 필요한 데이터만 골라서 전송 가능 (가벼움)                       |
  ***
  **예시)**
  - Entity(DB 모델)
  ```java
  @Entity
  public class User {
      private Long id;
      private String name;
      private String email;
      private String password;
  }
  ```
  - DTO( 데이터 전달 객체) with lombok
  ```java

  @Getter
  @AllArgsConstructor
  public class UserResponseDTO {
      private String name;
      private String email;
  }
  ```
  ⇒ password 같은 민감정보는 제외하고, 필요한 정보만 담아서 응답 가능.
  ***
  **번외)**
  장고로 백엔드 입문했을때 DTO라는걸 들어본적이 없었다. 대신 시리얼라이저라는걸 썼었는데 이거에 관해서 궁금증이 생겨 더 찾아보았다.
  Django에서는 **Serializer**가 사실상 **DTO 역할**을 한다.
  근데 데이터만 전달하는게 아니라 직렬화도 해주고 유효성 검사도 해주는 유틸들이 붙어있다.
  | 기준               | Java(Spring)               | Python(Django)                            |
  | ------------------ | -------------------------- | ----------------------------------------- |
  | 데이터 전달용 객체 | DTO 클래스                 | Serializer 클래스                         |
  | 역할               | 요청/응답 데이터 구조 정의 | 모델 데이터 → JSON 변환, JSON → 객체 검증 |
  | 자동 변환 지원     | 없음 (수동 매핑)           | 있음 (DRF가 자동 처리)                    |
