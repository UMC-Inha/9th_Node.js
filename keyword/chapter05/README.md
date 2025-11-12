- 환경 변수
  참고: https://velog.io/@overslept/%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-%ED%99%98%EA%B2%BD%EB%B3%80%EC%88%98 / https://inpa.tistory.com/entry/EXPRESS-%F0%9F%93%9A-dotenv-%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4

  ***

  ## 환경변수란 무엇인가?

  환경변수(Environment Variables)는 **운영체제나 애플리케이션이 실행되는 환경에서 설정할 수 있는 동적인 값이**다. 코드에 하드코딩하지 않고 외부에서 값을 주입할 수 있게 해준다.

  - 일반적으로 **API 엔드포인트, 비밀 키, 접근 토큰** 등의 민감 정보나 배포 환경마다 다른값을 설정한다.
  - OS나 런타임에 저장된 **전역적인 설정값**이다.

  ***

  ## 환경변수의 역할

  | 상황                 | 예시                                   | 설명                                                           |
  | -------------------- | -------------------------------------- | -------------------------------------------------------------- |
  | **보안 정보 관리**   | `DB_PASSWORD`, `JWT_SECRET`, `API_KEY` | GitHub 같은 곳에 비밀번호/토큰이 노출되지 않도록 `.env`에 저장 |
  | **환경별 설정 분리** | `NODE_ENV=development` / `production`  | 개발·테스트·운영 환경별로 다른 설정을 쉽게 구분                |
  | **배포 환경 제어**   | `PORT=8080`, `LOG_LEVEL=debug`         | 서버 실행 포트, 로깅 수준 등 배포 시점에 동적으로 설정         |
  | **클라우드 연동**    | `AWS_ACCESS_KEY_ID`, `S3_BUCKET_NAME`  | 외부 서비스 자격 증명 관리                                     |

  ***

  ## 환경변수를 사용해보자~ Feat. dotenv

  **dotenv**은 환경변수를 다루는 모듈이다.
  아래 명령어를 통해 dotenv 의존성을 설치할 수 있다.

  ```jsx
  npm install dotenv --save
  ```

  1. .env 파일 생성 후 내가 사용하고자 하는 키들을 환경 변수로 등록해둔다
     ![image.png](attachment:5843caa7-ef98-4b7c-93e6-5f829ce573eb:image.png)
  1. dotenv를 import해서 해당 환경 변수를 꺼내올수있다.

  ```jsx
  import dotenv from "dotenv"; // 꼭 상단에 dotenv import 해주기
  dotenv.config(); // dotenv 이용해서 환경 변수 설정하기

  console.log(process.env.TEST_KEY);
  ```

  1. .gitignore에 꼭 .env 파일을 넣어두는걸 잊지말자.

  ```jsx
  .env
  ```

- CORS
  참고:https://teddy0.tistory.com/7[7](https://teddy0.tistory.com/7) / https://docs.tosspayments.com/resources/glossary/cors

  ***

  ### CORS란?

  ![CORS 에러](attachment:a83067e4-1dd4-4f29-a47b-3190d731abcf:image.png)
  CORS 에러
  CORS == **(Cross-Origin Resource Sharing, 교차 출처 리소스 공유)**

  > **서로 다른 출처(Origin) 간의 리소스 요청을 허용하거나 차단하는 브라우저 보안 정책**

  ***

  ### 출처(Origin)란?

  | 요소           | 예시                  | 설명                 |
  | -------------- | --------------------- | -------------------- |
  | 프로토콜       | `http://`, `https://` | 보안 연결 여부       |
  | 도메인(호스트) | `myshop.com`          | 서버 주소            |
  | 포트 번호      | `:3000`, `:8080`      | 프로세스 구분용 포트 |

  ⇒ 이 세 가지 중 **하나라도 다르면** “다른 출처(Cross-Origin)로 간주된다.
  **예시.**

  > https://www.myshop.com → http://www.myshop.com:8080
  >
  > 프로토콜(`https` vs `http`)과 포트(`443` vs `8080`)가 다르기 때문에 **CORS 에러 발생**.

  ***

  ### 이런게 왜 생겼을까?

  초기 웹에서는 프론트와 서버 **모두 같은 도메인에서 처리**됐기 때문에
  다른 출처의 리소스를 접근하는 건 “해킹 시도”로 간주됐다. ⇒ 기본적으론 **(SOP, Same-Origin Policy)**
  하지만 지금은 프론트엔드와 백엔드가 분리된 구조가 일반적이다.
  그래서 요런식으로 프론트랑 백 다른 도메인 가짐.

  ```
  프론트: http://localhost:3000
  백엔드: http://localhost:8080/api
  ```

  이 경우 서로 다른 출처이므로 브라우저의 기본 정책**(SOP, Same-Origin Policy)에** 의해 API 요청이 차단됨. ⇒ 이를 허용하기 위해 만들어진 것이 바로 “**CORS 정책”**
  | 구분 | 의미 | 역할 |
  | ---------------------------- | --------------------- | --------------------------------------------------- |
  | **SOP (Same-Origin Policy)** | 동일 출처 정책 | 출처가 다르면 리소스 접근 차단 |
  | **CORS** | 교차 출처 리소스 공유 | 예외적으로 허용할 수 있도록 서버에서 허가 헤더 설정 |
  ⇒ 서버단에서 CORS ALLOW ORIGINS 도메인들을 설정해둔다
  ⇒ 해당 도메인으로부터 오는 요청은 허용, 그 외에는 CORS Error

  ***

  ### Express에서 CORS 설정법

  1. 패키지 설치

  ```jsx
  npm install cors
  ```

  1. 서버단 CORS 설정

  ```jsx
  import express from "express";
  import cors from "cors";

  const app = express();

  // CORS 설정
  app.use(
    cors({
      origin: "http://localhost:3000", // 허용할 프론트 주소
      credentials: true, // 쿠키/세션 허용 시 필요
    })
  );
  ```

  1. http://localhost:3000 도메인에서 오는 요청은 허용. 그 외에는 CORS ERROR

  ***

  ### CORS의 내부 동작?

  브라우저는 본 요청을 하기전에 **Preflight Request(사전 요청)**을 보내 서버와 잘 통신 가능한지
  요청을 보낸다.
  ⇒ 서버가 사전 요청에 허락 응답 하면 그때 본요청을 보냄.

  ```jsx
  **(서버의 CORS 허용 응답 헤더: cors 미들웨어에서 자동으로 붙여준다)
  HTTP/1.1 200 OK
  Access-Control-Allow-Origin: http://localhost:3000
  Access-Control-Allow-Methods: GET, POST, PUT, DELETE
  Access-Control-Allow-Headers: Content-Type, Authorization
  Access-Control-Allow-Credentials: true**
  ```

  여기서 Preflight(사전 요청) 은 사람이 직접 보내는 게 아니라 브라우저가 자동으로 보내는 내부 동작이다.
  ![image.png](attachment:9981e988-2b72-41fc-9517-92f9b4d6c24d:image.png)
  이러는 이유는 CORS 요청 자체가 유저 데이터에 영향을 줄 수 있기 때문이다.
  **( 이전에 쓰던 simple Request ⇒ 본요청에 CORS 합쳐서 보냄)**
  ⇒ 이제는 Preflight Request 씀 안전해서
  **+ CORS 에러는 언제 나냐?**
  ⇒ Preflight Request가 실패 됐을때
  ⇒ 본 요청을 보내도 응답헤더 검증 실패
  이 두 경우 모두 남 ⇒ 둘다 브라우저가 허용되지 않은 출처라고 판단하기 때문

  ***

  ### CORS 해결 by Proxy

  **Proxy란** 중개 서버 ⇒ 요청을 중간에서 전달해준다!
  **프록시 = 브라우저를 속이는 것**
  이라고 생각해도 된다. 실제 온 요청 도메인을 다른 도메인으로 바꿔치기해서 속이는 느낌.
  Nginx에서도 가능하고,
  프론트의 경우 배포를 Vercel로 하면 Vercel의 프록시에서도 이렇게 우회가 가능하다.
  ![image.png](attachment:3e40712d-109c-4b7d-8d2e-6daa024e653c:image.png)

  ***

  ### 결론

  결론적으로 CORS 에러의 해결책은 두가지다.

  1. 서버에서 CORS ALLOW ORIGIN에 프론트 도메인 주소를 추가하는것.
  2. 프록시로 프론트 도메인을 서버와 같은 도메인인거처럼 만들어서 우회

- DB Connection, DB Connection Pool
  참고: https://velog.io/@lilychoi/DB-Connection-Pool-%EC%9D%B4%EB%9E%80 / https://hongseob.tistory.com/98#%08DBCP%20(Database%20Connection%20Pool)-1

  ***

  ### 들어가기에 앞서…

  백엔드 서버와 DB의 통신**(DB Connection)**은 TCP 연결을 기반으로 한다.
  ⇒ hand shaking 해야돼서 커넥션 열고 끊을때마다 복잡한 과정 반복. ⇒ 비용이 증가한다.
  이 TCP 커넥션 여닫는 비용을 절감하기 위해 **나온것이 “DB Connection Pool”**
  DBCP는 커넥션을 미리 연결해놓고 Pool에다가 보관을 해둔다. ⇒ 직접 커넥션을 열고 닫는게 아닌 이 Pool에서 필요할때마다 빌려쓰고 반환하는 방식.

  ***

  ### 기존의 DB Connection

  ![기존 방식](attachment:8314dc3b-4cbd-49de-9cdc-84838274adbd:image.png)
  기존 방식
  기존 방식은 DB에 쿼리문을 보낼 때마다 Connection을 하고 결과를 받아오는 방식.
  기존 방식은 여러 클라이언트로부터 동시 요청 수가 많아져 DB의 수용범위를 벗어날 때 문제가 생겼음
  ⇒ 애초에 요청할때 연결과정 거쳐서 리소스 낭비가 너무 심하고 초과시 연결 실패나 요청 드랍 등등…

  ***

  ### **DB Connection Pool**

  그래서 등장한게 연결을 재사용하는 DB Connection Pool
  ![Connection Pool 방식](attachment:6ed9cc58-9ea0-4be1-82a4-8f36098bf6c5:image.png)
  Connection Pool 방식

  - DB의 **최대 연결 수를 안정적으로 유지** (예: 항상 10개만 사용)
  - 요청이 몰리면 Pool이 큐에 넣어 순서대로 처리 (대기하지만 드랍은 안 됨)
  - 연결 생성/해제 오버헤드도 거의 없음
    ⇒ DB의 부담을 줄이고 안정적으로 빠르게 요청 처리 가능
    단점이 있다면 커넥션이 없어도 연결을 유지 해야된다는 것.

  ### Connection Pool 이 커지면 성능이 무조건 좋아질까?

  ### 커넥션 풀을 크게 설정하면

  - 많은 메모리를 사용하지만, 동시에 많은 사용자가 대기 시간이 줄어들어 성능이 향상될 수 있다.

  ### 커넥션 풀을 작게 설정하면

  - 메모리 소모는 줄어들지만, 동시 접속자가 많아지면 대기 시간이 길어질 수 있다.
    따라서 커넥션 풀의 크기를 적절히 조절하여 최적의 성능을 유지해야 한다.

  ```jsx
  (참고) Hikari CP는 적절한 Connection Pool의 크기를
  1 connections = ((core_count) * 2) + effective_spindle_count) 로 정의하고 있다.

  core_count (코어 수): 시스템에서 사용 가능한 CPU 코어의 수다.
  effective_spindle_count (유효 스핀들 수): 데이터베이스의 유효 디스크 스핀들(회전하는 디스크 드라이브)의 수
  ```

  ***

  ### 대표적인 커넥션풀 라이브러리

  ![스크린샷 2025-10-28 오후 3.54.34.png](attachment:1dd3f21b-cc45-4fbf-8e2d-c4aae4ba2b02:스크린샷_2025-10-28_오후_3.54.34.png)

- 비동기 (async, await)
  참고: https://trustmitt.tistory.com/85
  ***
  ### 자바스크립트에서의 동기와 비동기
  ![이런 느낌이다.](attachment:466b8b64-5b71-4792-bc7f-d1eb5650a38c:image.png)
  이런 느낌이다.
  ![Hol Blocking](attachment:e835f7c4-552f-49f6-9f60-ebc04ed9b6d8:image.png)
  Hol Blocking
  자바스크립트는 **싱글 스레드 기반이다.**
  그래서 하나의 쓰레드에서 모든 작업을 처리해야돼서, 한번에 하나의 작업만 가능하다.
  그래서 자바스크립트에서 동기 방식으로 처리하게 되면, 한번에 하나 씩만 작업이 가능해져서 큰일남.
  ⇒ 이러한 문제점을 해결하기 위해 JS에서는 **비동기 처리**가 필요하다.
  그래서 JS에는 **“이벤트 루프”라는 비동기 시스템**이 탑재되어있다.
  자바스크립트(Node.js)는 I/O 작업을 비동기로 처리하는 이벤트 루프 구조 덕분에, 싱글 스레드임에도 동시에 많은 요청을 효율적으로 처리할 수 있다.
  PS.
  다른 멀티 스레드 언어들이랑 비교했을때 어떤가 싶었는데,
  요청마다 스레드를 만들어서 처리가능.
  **멀티스레드는 동시에 여러 일은 가능하지만 관리가 복잡하고 무겁다.**
  | 상황 | Node.js (싱글 + 비동기) | Java/Python (멀티스레드) |
  | --------------------------------------- | ------------------------- | ------------------------ |
  | **많은 네트워크 요청 처리** | ✅ 매우 효율적 (I/O 중심) | ❌ 스레드 부담 큼 |
  | **실시간 API, 채팅, 스트리밍** | ✅ 최적 | ❌ 상대적으로 느림 |
  | **복잡한 연산/CPU 작업** | ❌ 병목 생김 | ✅ 스레드 분산으로 유리 |
  | **DB, 파일, 외부 API 호출 많은 서비스** | ✅ 이상적 | ❌ 비효율적 |
  즉, Node.js는 I/O 중심 서버에 최적화된 구조다.
  ***
  ### Async과 Await
  ```
  async와 await는 ES2017(ECMAScript 8)부터 추가된 자바스크립트의 비동기 처리 방식 중 하나이다. async와 await를 사용하면 비동기 코드를 동기 코드처럼 작성할 수 있어, 가독성이 좋아지고 에러 처리가 간단해진다.
  async / await는 Promise를 더 편하게 쓰기 위한 문법적 설탕(syntactic sugar)
  내부적으로 여전히 Promise 기반 비동기 처리가 일어나고 있다.
  ```
  **async**는 함수의 앞에 붙여서 해당 함수가 비동기 함수임을 나타내며,
  **await**는 비동기 함수의 실행 결과를 기다리는 키워드이다.
  async 함수 안에서 await 키워드를 사용하면, 해당 비동기 작업이 완료될 때까지 코드 실행을 일시 중지하고 결과를 기다린 다음, 해당 결과를 반환한다.
  ```jsx
  async function getData() {
    try {
      const response = await fetch("/api");
      const data = await response.json();
      return data;
    } catch (error) {
      console.error(error);
    }
  }
  ```
  await 키워드는 Promise 객체가 완료될 때까지 코드 실행을 일시 중지하므로,
  **try-catch** 블록 안에서 사용하여 에러 처리를 할 수 있다.
  fetch에서 네트워크 에러가 발생할 경우, await 이후의 코드는 실행되지 않으며,
  catch 블록으로 제어가 넘어가 에러를 처리할 수 있다.
- try/catch/finally
  출처: https://devhoho.tistory.com/7
  ***
  ### 예외처리 (try/catch/finally)
  ```jsx
  try {
    // 실행할 코드 (여기서 에러가 날 수도 있음)
  } catch (error) {
    // 에러가 발생했을 때 실행되는 부분
  } finally {
    // 에러가 나든 안 나든 항상 실행되는 부분 (선택)
  }
  ```
  1. **try** 블록은 **예외가 발생할 가능성이 있는 코드**를 둘러싸는 데 사용된다. 예를 들어, 외부 API에서 데이터를 가져오거나, 사용자 입력을 처리할 때 오류가 발생할 수 있다.
  2. **catch** 블록은 try 블록에서 발생한 **오류를 포착하고 처리한**다. 이 블록은 오류에 대한 세부 정보를 담은 객체를 전달받아, 개발자가 원하는 대로 오류를 처리할 수 있게 해준다.
  3. **finally** 블록은 try 블록이 실행된 후에 **항상 실행되는 코드** 부분이다. 이 블록은 오류가 발생하거나 발생하지 않았을 때 모두 실행되며, 주로 리소스를 정리하거나 필수적인 마무리 작업을 위해 사용된다.
  ***
  ### JS의 예외처리 특성
  JavaScript에서 **try** 블록 안에 **return** 문이 있을 경우, **finally** 블록은 여전히 실행된다.
  ```jsx
  function exampleFunction() {
    try {
      return "try 블록";
    } finally {
      console.log("finally 블록 실행");
    }
  }
  // finally 블록 실행
  // 'try 블록'
  ```
  위 함수를 호출하면, 콘솔에는 "**finally 블록 실행**"이 출력되고, 함수는 "**try 블록**"을 반환한다.
  **finally가 먼저 출력 되고 그 다음 try문의 return이 실행 된다.**
  **그렇다면 finally 블록 안에 return 문이 있다면?**
  ```jsx
  function exampleFunction() {
    try {
      return "try 블록";
    } finally {
      return "finally 블록";
    }
  }
  // finally 블록
  ```
  위 함수를 호출하면, "**finally 블록**"이 반환된다. 이는 finally 블록이 try 블록보다 **우선순위가 높기** 때문이다.
  ***
  ### **실용적인 예제**
  이러한 동작 방식은 예외 처리 시 매우 유용할 수 있다.
  ```jsx
  function databaseQuery() {
    let connection;
    try {
      connection = openDatabaseConnection();
      // 데이터베이스 쿼리 수행
      return "쿼리 결과";
    } catch (error) {
      // 오류 처리
      throw error;
    } finally {
      closeDatabaseConnection(connection); // 이러면 무조건 DB 커넥션은 끊김
    }
  }
  ```
  예를 들어, 리소스를 정리하거나 데이터베이스 연결을 닫는 등의 작업을 **finally** 블록에서 수행할 수 있다.
  이렇게 하면 오류가 발생하든, 발생하지 않든 간에 해당 작업이 항상 실행되기 때문.
  PS. finally를 써본적이 많이 없는데 이렇게 유용한것이었다니…
