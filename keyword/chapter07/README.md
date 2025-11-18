- 미들웨어
    
    ## 미들웨어란 (큰 의미)
    
    미들웨어는 **양 쪽을 연결**하여 데이터를 주고 받을 수 있도록 **중간에서 매개 역할**을 하는 소프트웨어, 네트워크를 통해서 연결된 여러 개의 컴퓨터에 있는 많은 프로세스들에게 어떤 서비스를 사용할 수 있도록 연결해 주는 소프트웨어를 말한다. 3계층 클라이언트/서버 구조에서 미들웨어가 존재한다. 웹 브라우저에서 데이터베이스로부터 데이터를 저장하거나 읽어올 수 있게 중간에 미들웨어가 존재하게 된다.
    
    ## express에서 미들웨어
    
    미들웨어는 요청과 응답 사이에서 중간 역할을 하는 함수나 소프트웨어를 말한다.
    즉, 요청이 들어왔을 때, 본격적인 로직(라우트 처리 등)에 도달하기 전에 요청을 가공하거나 검사하는 중간 단계 코드이다.
    
    ```tsx
    import express from "express";
    const app = express();
    
    // 미들웨어
    app.use((req, res, next) => {
      console.log(`요청이 들어왔습니다: ${req.method} ${req.url}`);
      next(); // 다음 미들웨어나 라우터로 이동
    });
    
    app.get("/", (req, res) => {
      res.send("Hello World");
    });
    
    app.listen(3000);
    
    ```
    
    ## Nest에서 request 라이프사이클
    
    ![image.png](attachment:6177c47b-09e3-4a62-9655-30579ca3349a:image.png)
    
    1. request
    2. 미들웨어 (express와 동일)
    3. 가드: 컨트롤러/라우트 단에서 보통 인증/인가 로직을 담당
    4. 인터셉터(pre-controller): 컨트롤러 실행 전/후를 가로채 추가로직 실행
        1. 로깅, 캐싱, 응답 데이터 변환 등 활용
    5. 파이프: 요청 데이터의 검증과 변환 담당(dto 클래스와 함께 자주 사용)
    6. 컨트롤러
    7. 서비스
    8. 인터셉터(post-controller)
        1. 응답 형식 감싸기 등
    9. exception filter: 전역 예외 처리 등
    10. 서버 응답
    
- HTTP 상태 코드
    
    
    ## 200번대: 성공
    
    - 201: 요청 결과로 서버에 리소스가 성공적으로 생성
    - 204: 통신 결과는 성공, 응답 데이터는 별도 제공 X
    
    ## 300번대: 클라이언트 요청을 다른 리소스 혹은 URL로 유도하는 역할, 응답 결과에 location 헤더가 있으면, 그 위치로 자동 이동
    
    - 301: 영구적인 리다이렉션 - 특정 리소스의 URI가 영구적으로 이동
    - 302: 일시적인 리다이렉션 - 일시적인 변경
    - 304: 현재 요청하는 리소스가 변경되지 않았음 - 현재 클라이언트 측에 **캐싱**되어있는 리소스 그대로 사용하면 된다고 지시
    
    ## 400번대: 클라이언트 요청이 잘못되었음 + 어떻게 잘못되었는지
    
    - 400: 요청 매개변수 잘못 설정됨
    - 401: 잘못된 인증 정보
    - 403: 서버가 요청을 이해했지만 승인 거부(인증은 되었지만 인가가 되지 않음)
    - 404: 요청한 리소스 서버에 존재하지 않음
    - 405: http 메소드가 잘못 설정됨
    - 431: 헤더 크기가 너무 큼
    
    ## 500번대: 서버의 에러
    
    - 500: 서버 내부 오류
    - 503: 일시적인 서버 오류
    
    프론트에서 axios는 기본적으로 200-300 사이 코드만 resolve로 처리하고 나머지는 reject로 처리되기 때문에 모든 것을 200번으로 응답하면 resolve로 처리되기 때문에 디버깅이 힘들어짐
    
- 에러 핸들링(Error Handling)
    
    
    ## 에러
    
    프로그램이 예상치 못한 문제 또는 잘못되 동작을 발견할 때 동작하는 오류나 이슈
    
    ## 종류(발생 시점)
    
    - 컴파일 에러: 컴파일 동안 발생
    - 런타임 에러: 프로그램이 실행 중인 상태에서 발생하는 예외
    
    ## 에러 핸들링이 필요한 이유
    
    (1) 카카오 로그인 시 버튼 클릭했는데 아무 일이 일어나지 않음
    
    ![image.png](attachment:b048ab0a-93f0-464e-9db0-1e0fb0de5ef5:image.png)
    
    (2) 빨간 글씨로 오류임이 알려짐
    
    ![image.png](attachment:9202d525-eec0-4148-a7e3-cd95254f1dae:image.png)
    
    효과
    
    - 사용자에게 에러에 대한 상황을 인지시켜 서비스에 대한 안정성, 신뢰성, 부정적 경험 방지
    - 초기 리소스 비용은 있지만, 서비스 트랜잭션에 영향이 생겨 장애가 발생하는 상황을 예방하여 디버깅과 유지 보수 용이, 보안 강화까지 이뤄냄
    
    ## 에러 핸들링 기본
    
    Express에서는 미들웨어(Middleware)를 이용해 요청/응답 처리
    
    ```jsx
    app.use((err, req, res, next) => {
      console.error(err.stack); // 에러 로그 출력
      res.status(500).json({ message: 'Internal Server Error' });
    });
    
    ```
    
    > app.use()로 등록하고, 인자가 (err, req, res, next) 형태여야 Express가 자동으로 에러 핸들러로 인식
    > 
    
    ## 에러 발생시키기 - 수동으로 호출
    
    Express에서 에러를 전파하려면 **`next(err)`**를 호출합니다.
    
    ```jsx
    app.get('/user', (req, res, next) => {
      const user = null;
      if (!user) {
        const err = new Error('User not found');
        err.status = 404;
        return next(err); // 에러 핸들러로 전달
      }
      res.json(user);
    });
    3. 에러 핸들링 미들웨어의 흐름
    ```
    
    ## 에러 형식 통일하기 (Custom Error 클래스)
    
    ```jsx
    class AppError extends Error {
      constructor(message, status) {
        super(message);
        this.status = status;
        this.isOperational = true; // 운영상 에러 (로직 에러)
      }
    }
    
    // 예시 사용
    app.get('/', (req, res, next) => {
      next(new AppError('Not Found', 404));
    });
    
    // 에러 미들웨어
    app.use((err, req, res, next) => {
      const status = err.status || 500;
      res.status(status).json({
        success: false,
        message: err.message,
      });
    });
    
    ```