# 미션 1
## 회원가입
# 1️⃣ POST /api/v1/auth/sign-up

# 2️⃣ Request

[Request Header](https://www.notion.so/27fb57f4596b80769529ead21372c482?pvs=21)

```json
Content-Type: application/json
```

[Request Body](https://www.notion.so/27fb57f4596b80c2a614ebade60dfd64?pvs=21)

```json
{
	"nickname": "nickname"
	"email": "a@a.com"
	"password": "password"
}
```

# 3️⃣ Response

### ✅ 성공

[Response Body](https://www.notion.so/27fb57f4596b808b92bccdd25332bddb?pvs=21)

예시 응답

```json
{
  "success": true,
  "code": "S200",
  "message": "회원가입이 완료되었습니다.",
  "data": {
    "id": 1,
    "name": "전하경",
  }
}
```

### ❌ 실패

이메일 중복

```json
{
	"success": false,
	"code": "E4001",
	"message":"이미 존재하는 이메일입니다.",
	"data":null
}
```

서버 내부 오류

```json
{
	"success": false,
	"code": "E5000",
	"message":"서버 내부 오류가 발생했습니다.",
	"data":null
}
```

## 미션 진행률
# 1️⃣ GET /api/v1/mission/progress

# 2️⃣ Request

[Request Head](https://www.notion.so/27fb57f4596b80ef8689f0b1b3116861?pvs=21)

```json
Authorization: Bearer ey......
```

# 3️⃣ Response

### ✅ 성공

[Response Body](https://www.notion.so/27fb57f4596b806ba97febe58df80cfa?pvs=21)

예시 응답

```json
{
  "success": true,
  "code": "S200",
  "message": "현재 해당 Area에서 완수한 미션 개수입니다.",
  "data": {
	  "numSuccess": 3
  }
}
```

### ❌ 실패

로그인 토큰 없거나 만료

```json
{
	"success": false,
	"code": "E4001",
	"message":"Unauthorized",
	"data":null
}
```

서버 내부 오류

```json
{
	"success": false,
	"code": "E5000",
	"message":"서버 내부 오류가 발생했습니다.",
	"data":null
}
```
## 미션 목록 조회 - 새로운 미션들
# 1️⃣ GET /api/v1/missions/new

# 2️⃣ Request

[Request Head](https://www.notion.so/27fb57f4596b807f912af9f5b4e574f0?pvs=21)

```json
Authorization: Bearer ey......
```

# 3️⃣ Response

### ✅ 성공

[Response Body ](https://www.notion.so/27fb57f4596b80eb97d1f3fb42a740c0?pvs=21)

예시 응답

```json
{
  "success": true,
  "code": "S200",
  "message": "현재 해당 Area에서 완수한 미션 개수입니다.",
  "data": {
	  "restaurant_id": 1,
	  "price": 10000,
	  "point": 500,
	  "deadline": "2025-09-30"
	  "created_at": "2025-09-20 00:00:00"
  }
}
```

### ❌ 실패

로그인 토큰 없거나 만료

```json
{
	"success": false,
	"code": "E4001",
	"message":"Unauthorized",
	"data":null
}
```

서버 내부 오류

```json
{
	"success": false,
	"code": "E5000",
	"message":"서버 내부 오류가 발생했습니다.",
	"data":null
}
```

## 미션 도전
# 1️⃣ POST /api/v1/missions/new/:id

# 2️⃣ Request

[Request Head](https://www.notion.so/27fb57f4596b80faa1f0cbdfb8fcad31?pvs=21)

```json
Content-Type: application/json
Authorization: Bearer ey......
```

# 3️⃣ Response

### ✅ 성공

[Response Body](https://www.notion.so/27fb57f4596b8064b862cf51c6f7bea2?pvs=21)

예시 응답

```json
{
  "success": true,
  "code": "S200",
  "message": "미션을 도전합니다.",
  "data": {
	  "mission_id": 1
  }
}
```

### ❌ 실패

로그인 토큰 없거나 만료

```json
{
	"success": false,
	"code": "E4001",
	"message":"Unauthorized",
	"data":null
}
```

미션 도전 눌렀는데 그와 동시에 미션이 삭제됐다면

```json
{
	"success": false,
	"code": "E406",
	"message":"도전 버튼 누른 순간 서버에서 미션 삭제되었습니다.",
	"data":null
}
```

이중 요청 오류

```json
{
	"success": false,
	"code": "E407",
	"message":"이중 요청 오류입니다.",
	"data":null
}
```

서버 내부 오류

```json
{
	"success": false,
	"code": "E5000",
	"message":"서버 내부 오류가 발생했습니다.",
	"data":null
}
```

## 미션 목록 조회 - 진행중
# 1️⃣ GET /api/v1/missions?status=progress

# 2️⃣ Request

[Request Head](https://www.notion.so/27fb57f4596b8030b6f6d227bed53ce0?pvs=21)

```json
Content-Type: application/json
Authorization: Bearer ey......
```

[Request Param](https://www.notion.so/27fb57f4596b803bbde0c6ecebb2af03?pvs=21)

```json
/api/v1/missions?status=progress
```

# 3️⃣ Response

### ✅ 성공

[Response Body](https://www.notion.so/27fb57f4596b80f69fe9deb005eb9ff5?pvs=21)

예시 응답

```json

{
  "success": true,
  "code": "S200",
  "message": "미션 목록 조회 성공",
  "data": {
    "missionList": [
      {
        "restaurant_id": 101,
        "price": 25000,
        "point": 50,
        "deadline": "2025-10-10",
        "created_at": "2025-10-01T10:00:00Z"
      },
      {
        "restaurant_id": 102,
        "price": 18000,
        "point": 30,
        "deadline": "2025-10-12",
        "created_at": "2025-10-02T11:30:00Z"
      },
      {
        "restaurant_id": 103,
        "price": 32000,
        "point": 70,
        "deadline": "2025-10-15",
        "created_at": "2025-10-03T09:45:00Z"
      }
    ]
  }
}

```

### ❌ 실패

로그인 토큰 없거나 만료

```json
{
	"success": false,
	"code": "E4001",
	"message":"Unauthorized",
	"data":null
}
```

서버 내부 오류

```json
{
	"success": false,
	"code": "E5000",
	"message":"서버 내부 오류가 발생했습니다.",
	"data":null
}
```

## 미션 목록 조회 - 완료
# 1️⃣ GET /api/v1/missions?status=success

# 2️⃣ Request

[Request Head (1)](https://www.notion.so/27fb57f4596b8012a70ceba909eda438?pvs=21)

```json
Content-Type: application/json
Authorization: Bearer ey......
```

[Request Param (1)](https://www.notion.so/27fb57f4596b805d81d8ed0bd5fc4d62?pvs=21)

```json
/api/v1/missions?status=success
```

# 3️⃣ Response

### ✅ 성공

[Response Body (1)](https://www.notion.so/27fb57f4596b80c39724c57522ff7e08?pvs=21)

예시 응답

```json

{
  "success": true,
  "code": "S200",
  "message": "미션 목록 조회 성공",
  "data": {
    "missionList": [
      {
        "restaurant_id": 101,
        "price": 25000,
        "point": 50,
        "deadline": "2025-10-10",
        "created_at": "2025-10-01T10:00:00Z"
      },
      {
        "restaurant_id": 102,
        "price": 18000,
        "point": 30,
        "deadline": "2025-10-12",
        "created_at": "2025-10-02T11:30:00Z"
      },
      {
        "restaurant_id": 103,
        "price": 32000,
        "point": 70,
        "deadline": "2025-10-15",
        "created_at": "2025-10-03T09:45:00Z"
      }
    ]
  }
}

```

### ❌ 실패

로그인 토큰 없거나 만료

```json
{
	"success": false,
	"code": "E4001",
	"message":"Unauthorized",
	"data":null
}
```

서버 내부 오류

```json
{
	"success": false,
	"code": "E5000",
	"message":"서버 내부 오류가 발생했습니다.",
	"data":null
}
```
## 성공 요청
# 1️⃣ PATCH /api/v1/missions/:id

# 2️⃣ Request

[Request Head ](https://www.notion.so/27fb57f4596b80398568cc32e68d3d0e?pvs=21)

```json
Content-Type: application/json
Authorization: Bearer ey......
```

[Path Variable](https://www.notion.so/27fb57f4596b80e2853ffe36db0592db?pvs=21)

# 3️⃣ Response

### ✅ 성공

[Response Body](https://www.notion.so/27fb57f4596b802da3caf6457847a8df?pvs=21)

예시 응답

```json
{
  "success": true,
  "code": "S200",
  "message": "미션 성공 요청 성공",
  "data": {
		"requestNumber": "1238493264"
	} 
}
```

### ❌ 실패

로그인 토큰 없거나 만료

```json
{
	"success": false,
	"code": "E4001",
	"message":"Unauthorized",
	"data": null
}
```

요청 전송했는데 동시에 삭제되었을 경우

```json
{
	"success": false
  "status": "E410",
  "message": "매장이 삭제되었습니다."
  "data": null
}
```

서버 내부 오류

```json
{
	"success": false,
	"code": "E5000",
	"message":"서버 내부 오류가 발생했습니다.",
	"data":null
}
```
## 리뷰 작성
# 1️⃣ POST /api/v1/missions/:id/review

# 2️⃣ Request

[Request Head ](https://www.notion.so/27fb57f4596b80139ff7ffd4850cb5ae?pvs=21)

```json
Content-Type: application/json
Authorization: Bearer ey......
```

[Path Variable ](https://www.notion.so/27fb57f4596b80d3afc9c35e940f106d?pvs=21)

[Request Body](https://www.notion.so/27fb57f4596b80de9534dbfd9af57f80?pvs=21)

# 3️⃣ Response

### ✅ 성공

[Response Body ](https://www.notion.so/27fb57f4596b80b8b294d7f8ad17ecd7?pvs=21)

예시 응답

```json
{
  "success": true,
  "code": "S200",
  "message": "리뷰를 성공적으로 작성했습니다.",
  "data": {
    "mission_id": 123,
    "score": 5,
    "content": "리뷰 내용",
    "img_url": [
      "https://a.com/img1.png",
      "https://a.com/img2.png"
    ],
    "created_at": "2025-10-01 18:00:00Z"
  }
}
```

### ❌ 실패

로그인 토큰 없거나 만료

```json
{
	"success": false,
	"code": "E4001",
	"message":"Unauthorized",
	"data": null
}
```

서버 내부 오류

```json
{
	"success": false,
	"code": "E5000",
	"message":"서버 내부 오류가 발생했습니다.",
	"data":null
}
```




- [x]  Soft Delete가 무엇인지 찾아보시고 soft delete에는 어떠한 HTTP Method가 들어가면 좋을지 적어주세요
    - 내용들을 간단하게 정리하여 주세요
        
        ## Soft delete
        
        데이터베이스에서 실제로 삭제되지는 않지만, 삭제되었다고 마킹이 남는 것으로, 나중에 다시 데이터를 복구시키거나 접근할 수 있다. 히스토리 조회나 회원 복구 등으로 유용하게 사용한다.
        
        - 장점: 필요하면 복구 가능 / 중요한 정보 잃기 방지
        - 단점: 필요한 데이터베이스 공간 증가 / deleted data를 쿼리하기 위해 더 복잡한 쿼리를 사용해야함
        
        ## Hard delete
        
        soft delete와 반대로 완전히 DB에서 지우는 것
        
        - 장점: 비교적 저장 공간 작고, DB 다루기 쉬움
        - 단점: 영구 삭제됨 / 변화 검증 혹은 추적 불가
        
        ## Patch인가 Delete인가!
        
        - 원칙적으로 보면 PATCH일 것 같지만, 사용자 관점에서 보면 DELETE가 의미론적으로 자연스러움
        - REST 원칙 > PATCH
        - 사용자 의도 > DELETE
        
        ## Http Method에 대한 내용
        
        https://datatracker.ietf.org/doc/html/rfc7231#section-4.3.5
        
- [x]  컨트롤 URI에 대해 조사해주시고 어떠할 때 사용이 가능한 지 예시를 들어 설명해주세요.
    - 내용들을 간단하게 정리하여 주세요
        
        ## Control URI의 개념
        
        단순한 데이터 CRUD가 아닌, 비즈니스 로직 프로세스를 실행하도록 만든 API 주소이다.
        
        단순 데이터 수정, 삭제가 아니다!
        
        ## 특징
        
        - 행위 기반: 기존 restAPI와 다르게 URI에 “행위”가 포함된다.
        - http 메소드에 제한이 없다. 일반적인 CRUD에서 벗어나 복합적이고 모호한 의미를 가지기 때문이다.
        - 단순 리소스 조작이 아닌, 업무 흐름에 따른 상태 전환이 목적인 것이다.
        
        ## 예시
        
        `/orders/{orderId}/start-delivery` 
        
    
     
    
- [x]  https://learn.microsoft.com/ko-kr/azure/architecture/best-practices/api-design - 문서를 읽고 주요 내용을 간단히 정리해주세요.
    - 내용들을 간단하게 정리하여 주세요
        - 익숙한 내용
            
            ## Restful 웹 API 원칙
            
            - 플랫폼 독립성: 클라이언트 내부 구현과 관계 없이 웹 API 호출 가능, 그러기 위해서 명확한 설명서 제공, json or xml과 같은 친숙한 데이터 교환 형식 지원
            - 느슨한 결합: 웹서비스와 클라이언트 서로는 각자 내부 구현을 알 필요 없음. 서로가 동의하는 데이터 교환 메커니즘 사용하고 표준 프로토콜만 사용
            
            ## REST API 디자인 개념
            
            - URI(유니폼 리소스 식별자): restapi는 리소스 중심으로 설계됨. 각 리소스는 해당 리소스를 고유하게 식별하는 uri로 표시됨
            - 상태 비지정 요청 모델: http 요청은 독립적, 어떤 순서로든 발생 가능 > 요청 간 일시적 상태 정보를 유지하는 것은 불가능 > 각 요청은 원자성 작업
            
            ## 리소스 URI 정의
            
            - URI는 명사(리소스) 기반. 동사는 피함
            - 컬렉션과 개별 리소스 구분 (컬렉션은 복수)
                - /orders와 /orders/1
            - 명명 규칙
                - 행동은 http 메소드로 표현
                - uri를 통해 리소스 간 관계 표현 ex) /customers/5/orders but 지나치게 깊은 계층은 피하기
                - 내부 DB 스키마 그대로 노출 X
                - 간단한 함수형 동작은 쿼리 스트링 형태로 노출 가능하지만, 남용 금지
            
            ## 메서드 정의
            
            | 메서드 | 용도 | 특징 |
            | --- | --- | --- |
            | **GET** | 리소스 조회 | 멱등성 있음, 응답 본문에 리소스 포함, 상태 코드: 200/204/404 |
            | **POST** | 새 리소스 생성 또는 처리 데이터 제출 | 서버가 URI 할당, 멱등성 없음, 상태 코드: 200/201/204/400/405 |
            | **PUT** | 리소스 전체 업데이트 또는 존재하지 않으면 생성 | 멱등성 있음, 상태 코드: 200/201/204/409 |
            | **PATCH** | 리소스 일부 업데이트 | 멱등성 보장되지 않음, JSON 패치/병합 패치 사용, 상태 코드: 200/400/409/415 |
            | **DELETE** | 리소스 삭제 | 멱등성 있음, 상태 코드: 204/404 |
            
            상태코드 정리
            
            - 200 OK: 요청 성공, 본문 포함 가능
            - 201 Created: 새 리소스 생성
            - 204 No Content: 성공했지만 본문 없음
            - 400 Bad Request: 잘못된 요청
            - 404 Not Found: 리소스 없음
            - 405 Method Not Allowed: 지원되지 않는 메서드
            - 409 Conflict: 리소스 상태 충돌
            - 415 Unsupported Media Type: 지원되지 않는 데이터 형식
            - 406 Not Acceptable: 클라이언트가 요청한 형식 지원 불가
        
        - 새로운 내용
            
            ## 비동기 메서드 구현
            
            언제 필요? 
            
            - 메서드를 완료하는데 시간이 걸리는 처리일 경우
            - 클라이언트에 응답 보내기 전에 완료될 때까지 기다릴 수 없는 경우!
            
            특징
            
            - http 상태 코드 202를 반환해서 요청이 처리에 허용됐지만 불완전함 나타내기
            
            ```json
            HTTP/1.1 202 Accepted
            Location: /api/status/12345 // 위치 헤더에 상태 엔드포인트 URI 포함
            ```
            
            흐름
            
            - 클라이언트 POST /jobs/start 요청
            - 서버는 즉시 202 응답 후 location 헤더 제공
            - 백그라운드에서 계속 작업 수행
            - 클라이언트 GET /jobs/status/:id 로 상태 확인 가능!
            - 작업이 완료되었다면 303 코드 및, 실제 결과 리소스를 확인할 수 있는 URI 제공
            
            - job.service.ts
                
                ```tsx
                // job.service.ts
                import { Injectable } from '@nestjs/common';
                
                interface Job {
                  id: string;
                  status: 'pending' | 'completed' | 'failed';
                }
                
                @Injectable()
                export class JobService {
                  private jobs: Record<string, Job> = {};
                
                  startLongTask(): string {
                    const jobId = Math.random().toString(36).substr(2, 9);
                    this.jobs[jobId] = { id: jobId, status: 'pending' };
                
                    // 비동기 작업 시작 (await 없이)
                    setTimeout(() => {
                      this.jobs[jobId].status = 'completed';
                      console.log(`Job ${jobId} completed!`);
                    }, 5000); // 5초 뒤 완료
                
                    return jobId;
                  }
                
                  getJobStatus(id: string): Job | undefined {
                    return this.jobs[id];
                  }
                }
                
                ```
                
            
            ```tsx
            @Controller('jobs')
            export class JobController {
              constructor(private readonly jobService: JobService) {}
            
              @Post('start')
              startJob(@Res() res: Response) {
                const jobId = this.jobService.startLongTask();
                // 202 상태코드 반환, location 헤더 전달
                res.status(202).location(`/jobs/status/${jobId}`).send({
                  message: 'Job started',
                  jobId,
                });
              }
            
            	// 완료된 결과 상태 확인
              @Get('status/:id')
              getJobStatus(@Param('id') id: string) {
                const job = this.jobService.getJobStatus(id);
                if (!job) return { status: 'not found' };
                return { status: job.status };
              }
            }
            ```
            
            클라이언트가 해당 엔드포인트에 GET 요청을 보낼 때 응답에는 요청의 현재 상태가 포함되어있어야 함. 필요에 따라서는 해당 작업을 취소하는 링크를 포함시킬 수 있음
            
            ```tsx
            HTTP/1.1 200 OK
            Content-Type: application/json
            
            {
                "status":"In progress",
                "link": { "rel":"cancel", "method":"delete", "href":"/api/status/12345" }
            }
            ```
            
            ## 부분 응답 지원
            
            ### 문제 상황
            
            - 일부 리소스가 **크고 이진 데이터**(이미지, 비디오, 파일 등)를 포함
            - 불안정한 네트워크나 큰 응답으로 인해 **다운로드 실패 가능성**
            - 전체 데이터를 한 번에 가져오면 **응답 지연** 발생
            
            ---
            
            ### 해결 방법: 부분 응답 지원
            
            (1) Accept-Ranges 헤더
            
            - 서버가 **부분 GET 요청**을 지원한다는 것을 나타냄
            - 클라이언트는 **바이트 범위(Range 헤더)**를 지정하여 일부 데이터만 요청 가능
            
            ```
            HEAD https://api.contoso.com/products/10?fields=productImage
            
            ```
            
            서버 응답 예시:
            
            ```
            HTTP/1.1 200 OK
            Accept-Ranges: bytes
            Content-Type: image/jpeg
            Content-Length: 4580
            
            ```
            
            - `Content-Length` = 전체 리소스 크기
            - `Accept-Ranges: bytes` = 부분 요청 가능 표시
            
            ---
            
            (2) 부분 GET 요청
            
            - 클라이언트는 필요한 범위만 요청
            
            ```
            GET https://api.contoso.com/products/10?fields=productImage
            Range: bytes=0-2499
            
            ```
            
            서버 응답 예시:
            
            ```
            HTTP/1.1 206 Partial Content
            Accept-Ranges: bytes
            Content-Type: image/jpeg
            Content-Length: 2500
            Content-Range: bytes 0-2499/4580
            
            ```
            
            - 상태 코드 **206 Partial Content**
            - `Content-Length` = 이번 응답의 실제 바이트 수
            - `Content-Range` = 반환된 데이터 범위 + 전체 크기
            - 후속 요청으로 나머지 데이터를 이어서 가져올 수 있음
            
            ---
            
            ### 추가 권장 사항
            
            - **HEAD 요청 구현**:
                - 실제 데이터를 가져오기 전, 리소스 크기와 부분 요청 가능 여부 확인
            - **작은 청크 단위로 데이터 전송**:
                - 안정성 향상, 실패 시 재요청 최소화
            
            ## HATEROAS 구현
            
            - 클라이언트가 API를 탐색할 수 있도록 응답에 링크 정보를 포함시키는 방법
            - Hypermedia As The Engine Of Application State > 하이퍼미디어를 통해 애플리케이션 상태 전환을 유도한다!
            
            장점
            
            - 클라이언트가 URL이나 엔드포인트 하드코딩 X, 서버가 제공하는 링크 따라가며 작업 수행
            
            ## 버전 관리 구현
            
            - 웹 API는 비즈니스 요구사항이 변경되면 새 리소스 컬렉션이 추가됨
            - 그에 따라 리소스 간의 관계 변경 가능, 데이터 구조 수정 가능
            
            방법
            
            - URI 버전 관리
                - /v1/customers/3
                - 캐시 친화적
                - 단점은 hateroas 구현 시 링크에 모두 버전 포함해야한다.
            - 쿼리 문자열 버전 관리
                - URI는 그대로, 쿼리 매개변수로 버전 지정
                - /customers/3?version=2
                - 단점은 일부 프록시/브라우저에서 캐시되지 않을 수 있다는 점!
            - 헤더 버전 관리: Custom-Header: api-version=2
                - 단점은 클라이언트가 모든 요청에 헤더 추가해야함
            - 미디어 유형 버전 관리: Accept: application/vnd.contoso.v1+json
            
            ## 다중 테넌트 웹 API
            
            단일 API에서 여러 조직(테넌트)이 리소스를 공유. 격리, 확장성, 사용자 맞춤화를 고려해 설계해야 함.
            
            - 테넌트 식별 방법:
                1. 하위 도메인/도메인 기반: `adventureworks.api.contoso.com`처럼 테넌트별 도메인 사용. DNS 구성 필요. 역프록시로 내부 URL 보호.
                2. HTTP 헤더 기반: `X-Tenant-ID` 또는 JWT 클레임에 테넌트 정보 포함. URI를 깔끔하게 유지 가능, 중앙 인증 가능, 캐시 관리 복잡.
                3. URI 경로 기반: `/tenants/adventureworks/orders/3`처럼 경로에 테넌트 포함. RESTful 디자인 손상 가능, 라우팅 복잡.
            - 영향: 엔드포인트 구조, 요청 처리, 인증/권한, API 게이트웨이 및 백엔드 라우팅에 영향.
        

