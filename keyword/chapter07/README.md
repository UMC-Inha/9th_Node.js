- 미들웨어
  https://expressjs.com/ko/guide/using-middleware.html
  https://lakelouise.tistory.com/211#google_vignette

  ***

  # 미들웨어란?

  ![image.png](attachment:5957eed6-5b3d-431f-95e1-bb56fcba288f:image.png)
  ![image.png](attachment:2c6d4892-869e-4a19-84a4-e32010aaa48d:image.png)
  **미들웨어(Middleware)란**

  > 요청(req)과 응답(res) 사이에서 실행되며, 공통 기능을 처리하는 “중간 함수”.
  > Express 애플리케이션은 **미들웨어 함수들의 연속 실행 구조**로 이루어진다.
  > 즉, Express는 미들웨어 체인 방식으로 동작한다.
  > 미들웨어는 다음과 같은 특징을 가진 함수:

  ```jsx
  (req, res, next) => { ... }
  ```

  미들웨어 함수는 다음 역할을 할 수 있다:

  1. 임의의 코드 실행
  2. req, res 객체 수정
  3. 요청–응답 주기를 끝내기(res.send 등)
  4. next() 호출해 다음 미들웨어로 이동
     **next()를 호출하지 않으면 요청 처리가 중단됨.**

  ***

  ## **1. 미들웨어의 작성법**

  - req, res, next를 가진 함수를 작성하면 해당 함수는 미들웨어로 동작할 수 있다.
  - `req` : HTTP **요청을** 처리하는 객체
  - `res` : HTTP **응답을** 처리하는 객체
  - `next` : 다음 미들웨어를 실행하는 함수

  ```jsx
  const auth = (req, res, next) => {
    if (!isAdmin(req)) {
      next(new Error("Not Authorized"));
      return;
    }
    next();
  };
  ```

  - req, res, next를 인자로 갖는 함수를 작성하면 미들웨어가 된다.
  - req, res 객체를 통해 HTTP 요청과 응답을 처리하거나 next 함수를 통해 다음 미들웨어를 호출해야 한다.
  - **next() 함수가 호출되지 않으면 미들웨어 사이클이 멈추기 때문에 주의해야 된다.**
  - 미들웨어는 적용되는 위치에 따라서 애플리케이션 미들웨어, 라우터 미들웨어, 오류처리 미들웨어로 분류가 가능하다.
  - 필요한 동작 방식에 따라 미들웨어를 적용할 위치를 결정해야 된다.

  ***

  ## 2. 전체 흐름

  ```jsx
  const app = express();
  const router = express.Router();

  /*
  ============================================================
    1. 애플리케이션 레벨 미들웨어
  ============================================================
  */

  app.use(logger); // (1) 모든 요청이 가장 먼저 여기로 들어온다.

  /*
  ============================================================
    2. 라우터 레벨 미들웨어
  ============================================================
  */

  // (3) "/admin" 요청이 들어오면 이 미들웨어 실행됨
  router.use(auth);

  // (4) auth 통과한 요청만 여기 도달
  router.get("/", (req, res) => {
    res.send("Hello Admin Router");
  });

  // (2) "/admin" 요청이 라우터 쪽으로 들어가는 진입점
  app.use("/admin", router);

  /*
  ============================================================
    4. 미들웨어 서브스택 (GET / )
  ============================================================
  */

  app.get(
    "/",
    logger, // 첫 번째 미들웨어
    (req, res) => {
      // 두 번째 (최종 라우트 핸들러)
      res.send("Hello Express Home");
    }
  );

  /*
  ============================================================
    5. 함수형 미들웨어 (커스텀)
  ============================================================
  */

  const roleAuth = (role) => {
    return (req, res, next) => {
      const userRole = req.query.role;
      if (userRole !== role) {
        return next(new Error(`Only ${role} allowed`));
      }
      next();
    };
  };

  app.get("/admin-only", roleAuth("admin"), (req, res) => {
    res.send("Admin Page");
  });

  /*
  ============================================================
    6. 오류 처리 미들웨어
    모든 next(err)는 여기로 바로 점프한다.
  ============================================================
  */

  app.use((err, req, res, next) => {
    console.error("오류 발생:", err.message);

    res.status(500).json({
      error: err.message || "Unknown error",
    });
  });

  /*
  ============================================================
    7. 서버 실행
  ============================================================
  */

  app.listen(3000, () => {
    console.log("Server running on http://localhost:3000");
  });
  ```

  ***

- HTTP 상태 코드
  https://developer.mozilla.org/ko/docs/Web/HTTP/Reference/Status
  ***
  # HTTP 상태 코드 정리
  HTTP 응답 상태 코드는 요청의 처리 결과를 나타내며, **1xx 정보**, **2xx 성공**, **3xx 리다이렉션**, **4xx 클라이언트 오류**, **5xx 서버 오류**로 구분된다.
  ```jsx
  app.get("/admin", (req, res) => {
    if (req.user.role !== "admin") {
      return res.status(403).json({ error: "Forbidden" });
    }
  });
  ```
  ***
  ## 1xx 정보 응답
  **100 Continue**
  요청의 첫 부분은 문제없으니 계속 진행하라는 뜻.
  **101 Switching Protocols**
  Upgrade 헤더에 따라 프로토콜 전환.
  **103 Early Hints**
  브라우저가 preload를 미리 시작하라고 힌트만 먼저 보내는 용도.
  ***
  ## 2xx 성공 응답
  **200 OK**
  요청 성공. 메소드에 따라 응답 내용 다름(GET이면 리소스 반환 등).
  **201 Created**
  새 리소스 생성 성공(POST, 일부 PUT).
  **202 Accepted**
  요청은 받았지만 처리 완료는 아님(비동기 처리).
  **203 Non-Authoritative Information**
  오리진이 아닌 다른 소스에서 가져온 메타데이터.
  **204 No Content**
  콘텐츠 없음. 보통 상태 업데이트 후 바디 없이 응답.
  **205 Reset Content**
  요청 처리 끝났으니 클라이언트 화면을 리셋하라는 뜻.
  **206 Partial Content**
  Range 요청을 통한 부분 응답. (파일 일부만 다운로드)
  ***
  ## 3xx 리다이렉션
  **300 Multiple Choices**
  여러 응답 중 선택 가능.
  **301 Moved Permanently**
  리소스의 URI가 영구 변경됨.
  **302 Found**
  일시적 이동. 다음 요청도 원래 URI 사용.
  **303 See Other**
  다른 URI로 GET 하라는 의미.
  **304 Not Modified**
  캐시된 버전 그대로 사용.
  **307 Temporary Redirect**
  리다이렉트하되 **HTTP 메소드(POST/GET)를 바꾸면 안 됨**.
  **308 Permanent Redirect**
  301과 유사하지만 메소드 변경 금지.
  ***
  ## 4xx 클라이언트 오류
  **400 Bad Request**
  문법 오류로 요청을 이해할 수 없음.
  **401 Unauthorized**
  인증 필요(토큰 없음/만료 등).
  **402 Payment Required**
  미래 결제 시스템용으로 예약한 코드. 사실상 쓰이지 않음.
  **403 Forbidden**
  접근 권한 없음. 서버는 누군지 알고 있음.
  **404 Not Found**
  리소스 없음. 가장 많이 보이는 코드.
  **405 Method Not Allowed**
  허용되지 않은 HTTP 메소드.
  **406 Not Acceptable**
  클라이언트가 Accept로 요구한 형식의 응답을 만들 수 없음.
  **407 Proxy Authentication Required**
  프록시 인증 필요.
  **408 Request Timeout**
  서버가 타임아웃으로 연결 종료.
  **409 Conflict**
  서버 현재 상태와 충돌(중복 데이터, 버전 충돌 등).
  **410 Gone**
  리소스가 영구적으로 삭제됨.
  **411 Length Required**
  Content-Length 누락.
  **412 Precondition Failed**
  전제조건이 맞지 않음.
  **413 Payload Too Large**
  요청 바디가 서버 한도보다 큼.
  **414 URI Too Long**
  URI 길이 초과.
  **415 Unsupported Media Type**
  지원하지 않는 미디어 타입.
  **416 Range Not Satisfiable**
  요청한 범위를 만족할 수 없음.
  **417 Expectation Failed**
  Expect 헤더의 조건 불충족.
  **418 I'm a teapot**
  에이프릴풀 기원. 의미 없는 테스트용 코드.
  **421 Misdirected Request**
  현재 서버가 해당 요청을 처리할 수 없음.
  **422 Unprocessable Entity**
  문법은 맞지만 의미적으로 처리할 수 없음.
  **423 Locked**
  리소스 잠김.
  **424 Failed Dependency**
  선행 요청 실패로 인해 현재 요청 실패.
  **426 Upgrade Required**
  프로토콜 업그레이드 필요.
  **428 Precondition Required**
  조건부 요청 필요(병행 수정 방지 목적).
  **429 Too Many Requests**
  요청 횟수 초과(rate limit).
  **431 Request Header Fields Too Large**
  요청 헤더가 너무 큼.
  **451 Unavailable For Legal Reasons**
  법적 사유로 접근 금지(검열된 페이지 등).
  ***
  ## 5xx 서버 오류
  **500 Internal Server Error**
  서버 내부 오류. 가장 일반적인 서버 오류.
  **501 Not Implemented**
  서버가 해당 HTTP 메소드를 아예 지원하지 않음.
  **502 Bad Gateway**
  게이트웨이 서버가 잘못된 응답을 받음.
  **503 Service Unavailable**
  서버 일시적 불가(과부하, 유지보수). Retry-After 권장.
  **504 Gateway Timeout**
  게이트웨이 서버에서 응답 타임아웃.
  **505 HTTP Version Not Supported**
  지원하지 않는 HTTP 버전.
  **506 Variant Also Negotiates**
  서버의 콘텐츠 협상 설정이 잘못되어 순환 참조 발생.
  **507 Insufficient Storage**
  서버 저장 공간 부족(WebDAV).
  **508 Loop Detected**
  무한 루프 감지(WebDAV).
  **510 Not Extended**
  요청 확장(옵션)이 더 필요함.
  **511 Network Authentication Required**
  공공 와이파이 로그인 페이지 같은 네트워크 인증 필요.
  ***
- 에러 핸들링(Error Handling)
  https://velog.io/@yenicall/What-is-Error-Handling
  https://velog.io/@yenicall/Node.js%EC%97%90%EC%84%9C%EC%9D%98-Error-Handling-%EC%A0%81%EC%9A%A9

  ***

  # 에러 핸들링(Error Handling) 정리

  ## 1. 에러 핸들링의 목적

  - 소프트웨어는 언제든지 에러나 예외가 발생한다.
  - 에러가 발생해도 프로그램이 갑작스럽게 종료되지 않도록 막고,
  - 사용자에게 적절한 안내를 제공하며 정상 흐름을 유지하기 위함이다.
  - 서비스 안정성과 사용자 경험을 위해 반드시 필요하다.

  ***

  ## 2. 에러 vs 예외

  ### 에러(Error)

  - 컴퓨터가 실행 과정에서 발생시키는 오류.
  - 문법 오류, undefined 참조, 네트워크 장애 등.
  - 개발자가 의도하지 않은 오류.
    예시:

  ```jsx
  consoel.log("오타");
  // ReferenceError: consoel is not defined
  ```

  ### 예외(Exception)

  - 개발자가 의도적으로 만들어내는 오류 상황.
  - 비즈니스 규칙을 위반했을 때 발생시킨다.
    예시:

  ```jsx
  if (!email.includes("@")) throw new Error("EMAIL_INVALID");
  if (password.length < 10) throw new Error("PASSWORD_INVALID");
  ```

  ***

  ## 3. 에러 핸들링 미들웨어의 필요성

  - 여러 곳에서 에러가 발생하므로 각 위치에서 처리하면 코드 중복이 생긴다.
  - 에러를 한곳에서 처리하는 방식이 유지보수에 유리하다.
  - Express에서는 에러 전용 미들웨어를 통해 모든 에러를 통합 처리한다.
    예시:
    ![image.png](attachment:3d5fcbb5-5bfe-4e2e-ac39-4d2f2175455d:image.png)
    사용 흐름:

  1. 컨트롤러에서 throw
  2. Express가 에러 미들웨어로 전달
  3. 하나의 장소에서 통합 처리

  ***

  ## 5. 왜 미들웨어로 모아서 처리하나?

  - 중복 제거
  - 유지보수 쉬움
  - 관심사의 분리(SOC)
  - 에러 로그를 중앙에서 관리
  - 모든 API 응답 형식을 통일할 수 있음

  ***

  ## 6. AsyncWarp

  1. Express는 원래 "동기 함수"만 에러를 자동으로 잡는다

  2. 근데 async 함수에서 throw 하면 Express가 못 잡는다

  3. 그래서 async 라우터를 try-catch로 감싸야 한다

  ```jsx
  app.get("/", async (req, res) => {
    try {
      await doSomething();
    } catch (err) {
      next(err);
    }
  });
  ```

  근데… 이걸 모든 라우터에 일일이 넣어야 한다.
  **매번 모든 컨트롤러에** 이렇게 반복되는 try-catch는 너무 귀찮고 지저분하다. 4. AsyncWrap = try-catch를 자동으로 만들어주는 함수

  ```jsx
  app.get('/user', asyncWrap(async (req, res) => {
    await getUser();     // throw 발생 → 자동으로 next(err)
    res.json(...);
  }));
  ```

  1. npm install express-async-handler 설치해서 써도 좋다.

  ***
