## <API 명세서 작성하기 미션>

![2주차 피드백 적용 ⇒ 미션 클리어 로그에 clear_status랑 created_at 필드 생성. ⇒ 미션 진행 상황 저장용](<attachment:1e6cf766-9045-4cb2-98ab-41ec1c7c426d:UMC_01_(2).png>)

2주차 피드백 적용 ⇒ 미션 클리어 로그에 clear_status랑 created_at 필드 생성. ⇒ 미션 진행 상황 저장용

- **참고자료**
  https://velog.io/@itissteam/restful-api-endpoints-well-written-guide
  https://coding-factory.tistory.com/1009
  https://romromlog.tistory.com/entry/%EB%AA%A8%EB%93%A0-%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-HTTP-%EC%9B%B9-%EA%B8%B0%EB%B3%B8-%EC%A7%80%EC%8B%9D-7-HTTP-%ED%97%A4%EB%8D%94-%EC%9D%BC%EB%B0%98-%ED%97%A4%EB%8D%94
  → 헤더에 정말 많은 정보를 담을수 있구나 싶었다. → 헤더에 관해서 좀 더 구체적으로 일단 필요한거만 알아봤다. → 추후 더 공부할 예정 → Request의 헤더랑 Response의 헤더랑 구성이 다르다. → 헤더 공부 이렇게 해본건 처음인데 그만큼 명료하게 전달해주는거라 협업할때 도움 많이 될것 같다.
  ![image.png](attachment:82841581-69e1-4b97-b69d-901605fc9b6a:image.png)
  → API 엔드포인트 만들때 버전(v1) 적는건 처음 알았는데, 프론트랑 협업할때 좋을것 같다.

### 홈 화면

![스크린샷 2025-09-30 오후 11.27.23.png](attachment:52bc0519-3b1d-482d-8322-9434d4112f27:6f2db5ce-6d2f-4f24-8f43-df08ade57bdb.png)

- **API Endpoint + (Path variable / query String )**
  **Path variable**
  ```sql
  안쓰임
  ```
  **query String**
  ⇒ 이걸로 해당 지역 관련 미션이랑 미션 카운트 검색
  ```sql
  home?region_id=10
  ```

⇒ 유저한테 지정된 지역이 하나 있는게 아니라, 지역별로 미션 카운트(다대다 관계) ⇒ 즉 쿼리 스트링으로 region_id를 지정해서 GET 해줘야함.

**region_id는 변하지 않는 고유키라 쿼리스트링에 쓰고, region_name은 변할 수 있는 표시용이라 응답에만 포함한다.**

```sql
GET /api/v1/home?region_id=10
```

- **Request Header**

헤더에는 로그인한 사용자의 Access Token이 **`Authorization: Bearer <Access Token>`** 형식으로 포함 ⇒ Body에서 따로 사용자 id를 안줘도 이걸로 누구의 홈 화면인지 식별 가능.

이렇게 네 개가 REST API에서 쓰는 기본 헤더 세트라고 함. → 더 쓰는것들 궁금함

```json
Host: api.umc.com                          //API 서버 도메인
Authorization: Bearer <Access Token>       //로그인 시 발급받은 Access Token
Content-Type: application/json             //요청 본문 타입 (GET이라 사실상 필요없다)
Accept: application/json                   //응답을 JSON으로 받고 싶다는 의사 표시
```

- **Request Body**

GET 요청이라 Request Body는 따로 없다.

```json
{}
```

---

- **Response Header**

클라이언트가 직접 적어 보내는 Request Header와 다르게.. ⇒ 서버가 응답하면서 기본 헤더를 자동으로 생성해준다고 한다. 다만 캐싱 정책이나 인증 관련 등등 특별한 경우에는 명세를 적는다 한다는데 보통 안적는다고 한다.

이런 내용이 포함된다고 한다. 2번부턴 생략하겠다.

```json
HTTP/1.1 200 OK                          // HTTP 버전 + 상태 코드 + 상태 메시지
Content-Type: application/json; charset=utf-8   // 응답 본문 데이터 형식
Content-Length: 256                      // 응답 본문 크기 (바이트 단위)
Date: Tue, 01 Oct 2025 13:41:00 GMT      // 서버가 응답을 생성한 시각
Server: nginx/1.25.3                     // 응답을 보낸 서버 정보
Cache-Control: no-store                  // 캐싱 금지 (클라이언트/프록시 모두 캐시하지 않음)
```

- **Response Body**
- **워크북 보면서 의문스러웠던점. ( 리소스 중심 패턴 VS Envelope 패턴 )**
  ⇒ 내가 지금까지 응답 바디에 담을때는 바디 안에 데이터만 담았지 상태 코드나 성공여부 같은걸 담은적이 없었다. 성공 여부는 200 OK 이런식으로 HTTP 상태코드랑 헤더로 표현 했었다. ⇒ 근데 워크북에는 바디 안에 상태 코드랑 성공여부랑 메세지 이런걸 담더라..? 이게 뭔가 싶어서 찾아보았다.
  https://jipang9-greedy-pot.tistory.com/121

  ### 리소스 중심 패턴 (Rest API 형식) ⇒ 내가 쓰던 방식

  ```json
  HTTP/1.1 200 OK

  {
    "username": "jin",
    "notifications": 3,
    "recentActivities": [ ... ]
  }
  ```

  - **특징**: HTTP 표준에 충실, 성공/실패 여부는 HTTP Status Code(200, 400, 500 등)로 표현.
  - **장점**: 단순하고 RESTful, 캐시·프록시 호환성 좋음.
    - → 중간에 있는 프록시/캐시 서버는 HTTP Status Code와 헤더만 보고 동작함. (바디 못읽음) → 이래서 인프라 관점에서 더 친화적이라고 한다.
  - **단점**: 에러 응답 모양이 엔드포인트별로 다를 수 있어 클라이언트 공통 처리 조금 번거로움.
    - → 예: `/users` API에서는 에러 JSON이 `{ "error": "Not found" }`
      `/orders` API에서는 `{ "msg": "없는 주문입니다." }`
      이런 식으로 제각각일 수 있음.

  ### Envelope(봉투) 패턴 ⇒ 워크북 방식

  ```json
  HTTP/1.1 200 OK // HTTP 상태코드를 안쓰는건 아님

  {
    "success": true,
    "code": "S200", // 이거 코드는 서버 내부에서 정한대로 하면됨.
    "message": "좋아요가 등록되었습니다.",
    "data": {
      "likeId": 15,
      "bookId": 7,
      "memberId": 2,
      "created_at": "2025-09-16T12:00:00"
    }
  }
  ```

  - **특징**: 모든 응답을 success/code/message/data 구조로 감싸서 통일.
  - **장점**: 클라에서 응답 처리 일관적, 서버 커스텀 코드(`S200`, `E401`)를 내려보내 디버깅/로깅 용이.
    - → 모든 응답이 `success/code/message/data` 구조라서
      프론트는 항상 `response.data.message`만 꺼내면 됨.
    - → HTTP 200인데도 상황별로 `S201`, `S202` 같은 서비스 코드 내려서 로직 분기 가능. (근데 이건 201 이런거 써도 가능하지 않나..? 이거 궁금함)
  - **단점**: HTTP Status Code와 정보가 중복될 수 있고, 구조가 불필요하게 깊어짐.
    - → 이미 200 OK인데 또 `success: true`, `code: S200` 넣음 → 같은 의미가 반복됨.
    - `response.data.username` → `response.data.data.username` 이런 식으로 중첩됨.
      ⇒ **HTTP 코드로 큰 틀**을 알려주고, **JSON 봉투로 세부 설명**을 해주는 구조. (둘다 씀)

  ### 결론

  - **작은 서비스 / REST 원칙을 강조하는 경우 ⇒ 단순, 빠름**
    **⇒ 리소스 중심 패턴**이 깔끔하고 표준적이라 추천
  - **중대형 서비스 / 다수 클라이언트(앱·웹·외부 연동) / 에러 로깅 중요 ⇒ 일관성, 확장성**
    ⇒ **Envelope 패턴**이 안정적이고 일관성 있어 관리 편함 → 프론트한테 편하다함
    ⇒ 근데 사실상 그냥 해당 프로젝트/회사에서 쓰는대로 쓰면 된다고 함.

미션은 워크북대로 Envelope 패턴으로 바디를 만들겠다.

**[ 성공 ]**

```json
HTTP/1.1 200 OK

{
  "success": true,
  "code": "S200",
  "message": "홈 화면 조회 성공!",
  "data": {
	  "region": { // 지역명
		  "id": 10,
		  "region_name": "안암동"
	  },
	  "user": { // 유저 정보
		  "id": 2,
		  "name": "기무진", // 이름은 혹시 몰라서 넣음
		  "total_point": 999999
		},
		"region_mission_user": { // 미션 카운터
			"mission_completed": 7, // region_mission_user.mission_counter => 지역에서 깬 미션 개수
			"missions_targeted": 10, // 목표 건수: 백엔드 로직안에 있는 상수
			"point_reward": 1000 // 보상: 백엔드 로직안에 있는 상수
		},
		"missions": [ // 미션 목록들 => 여러개라 배열에 담김
			{
				"diner_mission_id":101, // 첫번째 레코드
				"expire_date": "2025-10-08", // 이거 기반으로 D-DAY 계산
				"mission": {
					"id": 11,
					"quest": "10,000원 이상의 식사시",
					"reward": 500
				},
				"diner": {
					"id": 20,
					"diner_name": "반이학생마라탕",
					"food_type": "중식당"
				}
			},
			{
				"diner_mission_id":102, // 두번째 레코드
				"expire_date": "2025-10-08",
				"mission": {
					"id": 11,
					"quest": "10,000원 이상의 식사시",
					"reward": 500
				},
				"diner": {
					"id": 20,
					"diner_name": "반이학생마라탕",
					"food_type": "중식당"
				}
			}
		]
  }
}
```

**[ 인증 실패 ]**

```json
HTTP/1.1 401 Unauthorized

{
	"success": false,
	"code": "E401",
	"message": "인증이 만료되었습니다. 다시 로그인해주세요.",
	"data": null
}
```

**[ 서버 내부 오류]**

```json
{
  "success": false,
  "code": "E500",
  "message": "서버 내부 오류가 발생했습니다.",
  "data": null
}
```

### **마이 페이지 리뷰 작성**

![스크린샷 2025-10-01 오후 3.36.03.png](attachment:5981c44f-bc6b-4908-897f-88f8ce6f868d:스크린샷_2025-10-01_오후_3.36.03.png)

- **API Endpoint + (Path variable / query String )**
  **Path variable**
  ```sql
  없음
  ```
  **query String**
  ```sql
  없음
  ```

```sql
POST /api/v1/reviews
```

- **Request Header**

```sql
Host: api.umc.com                          // API 서버 도메인 (필수)
Authorization: Bearer <Access Token>       // 로그인 시 발급받은 Access Token (인증 필요할 때 필수)
Content-Type: application/json             // 요청 본문 타입 (POST/PUT/PATCH 때 필요, GET은 사실상 불필요)
Accept: application/json                   // 응답을 JSON으로 받고 싶다는 의사 표시 (자주 씀)
```

- **Request Body**

```json
{
  "diner_id": 20,
  "star": 4,
  "content": "너무너무 맛있다"
  // "created_at": => 이거는 클라가 주는게 아니라 DB에 insert할때 NOW()로 넣는다고 함. 안줘도됨.
}
```

---

- **Response**

[ 성공 ]

```json
HTTP/1.1 201 Created

{
	"success": true,
	"code": "S201",
	"message": "리뷰가 정상적으로 등록되었습니다.".
	"data": {
		"id": 301,
		"diner_id": 20,
		"user_id": 2,
		"star": 4,
		"content": "너무너무 맛있다",
		"created_at": "2025-10-01 시분초 밀리초",
		"updated_at": "2025-10-01 시분초 밀리초"
	}
}
```

[잘못된 요청] ⇒ 400 Bad Request ⇒ 평점이 갑자기 1~5값이 아닌게 왔을경우

```json
{
  "success": false,
  "code": "E400",
  "message": "1~5 사이의 별점을 입력해주세요.",
  "data": null
}
```

[ 인증 실패 / 서버 에러는 1번과 동일 ]

### **미션 목록 조회(진행중, 진행 완료)**

![스크린샷 2025-09-30 오후 11.31.15.png](attachment:0200eaf3-7950-4bec-9495-ddecd148416c:스크린샷_2025-09-30_오후_11.31.15.png)

- **API Endpoint + (Path variable / query String )**
  **Path variable**
  ```sql
  없음
  ```
  **query String**
  ```sql
  없음
  ```

⇒ 일단 미션 목록을 다 주고, 프론트 단에서 필터링해서 진행중/진행완료를 구분할지… 아니면 애초에 백엔드 엔드포인트를 두개써서 요청하는거 줄지 고민해봤음.

⇒ 아래와 같은 이유로 전자로 선택.

- API 호출이 한 번만 필요 → 네트워크 비용↓, 응답 속도↑
- 탭 이동 시 추가 요청 필요 없음 (UX 빠름)
- 서버/엔드포인트가 단순해짐 (관리 편함)

```sql
GET /api/v1/missions
```

- **Request Header**

```json
Host: api.umc.com                          //API 서버 도메인
Authorization: Bearer <Access Token>       //로그인 시 발급받은 Access Token
Content-Type: application/json             //요청 본문 타입 (GET이라 사실상 필요없다)
Accept: application/json                   //응답을 JSON으로 받고 싶다는 의사 표시
```

- **Request Body**

⇒ GET이라 리퀘스트 바디 없음

```json
{}
```

---

- **Response**

[성공]

```json
HTTP/1.1 200 OK

{
	"success": true,
	"code": "S200",
	"message": "내 미션 목록 조회 성공",
	"data": {
		"missions": [ // 미션 목록 배열 => 성공한거랑 진행중인거 섞여있어서 프론트 단에서 clear_status 필터링 해야함.
			{
				"diner_mission_id": 101, // 첫번째 레코드: 미션 상태 로그
				"expire_date": "2025-10-08",
				"clear_status": "COMPLETED",
				"mission": { // 미션 내용
					"id": 11,
					"quest": "12,000원 이상의 식사를 하세요!",
					"reward": 500
				},
				"diner": {
					"id": 20,
					"diner_name": "가게이름 a"
				}
			},
			{
				"diner_mission_id": 102, // 두번째 레코드: 미션 상태 로그
				"expire_date": "2025-10-08",
				"clear_status": "INPROGRESS",
				"mission": { // 미션 내용
					"id": 11,
					"quest": "12,000원 이상의 식사를 하세요!",
					"reward": 500
				},
				"diner": {
					"id": 20,
					"diner_name": "가게이름 a"
				}
			}
		]
	}
}


```

[ 권한 인증 실패 401 / 서버 에러 500 ]

⇒ 이런 목록 불러올때 또 어떤 예외 처리를 해주는게 좋을지 궁금하다.

### **미션 성공 누르기**

⇒ 이거 전부터 그랬는데.. 어떻게 작동하는 페이지인지 정확히 잘 모르겠다..

![스크린샷 2025-09-30 오후 11.32.16.png](attachment:04ec493f-d014-4b30-afe6-04f40f28a98c:스크린샷_2025-09-30_오후_11.32.16.png)

- **API Endpoint + (Path variable / query String )**
  **Path variable**

  ```sql
  /{dinerMissionId}/complete
  ```

  **query String**

  ```sql

  ```

⇒ 엔드 포인트에 해당 미션의 Path variable을 넣는다. ⇒ 해당 하는 미션 레코드의 미션 진행현황 바꿀수있음.

```json
POST /api/v1/missions/{dinerMissionId}/complete
```

- **Request Header**

```sql
Host: api.umc.com                          // API 서버 도메인 (필수)
Authorization: Bearer <Access Token>       // 로그인 시 발급받은 Access Token (인증 필요할 때 필수)
Content-Type: application/json             // 요청 본문 타입 (POST/PUT/PATCH 때 필요, GET은 사실상 불필요)
Accept: application/json                   // 응답을 JSON으로 받고 싶다는 의사 표시 (자주 씀)
```

- **Request Body**

```sql
{
  "auth_code": "920394810"   // 사장님이 제시한 구분 번호
}
```

---

- **Response**

[성공]

```json
HTTP/1.1 200 OK

{
	"success": true",
	"code": "S200",
	"message": "미션 성공했습니다.",
	"data": {
		"diner_mission_id": 101, // 해당 미션
		"mission": {
			"id": 11,
			"quest": "12,000원 이상의 식사",
			"reward": 500
		},
		"diner": { // 해당 가게
			"id": 20,
			"diner_name": "가게이름 "
		},
		"mission_clear_log": { // 해당 미션 clear_status: INPROGRESS => COMPLETED로 상태 변경
			"clear_status": "COMPLETED",
			"clear_at": "2025-10-01-시간밀리초"
		},
		"point_history": { // 포인트 히스토리에 포인트 변동사항 기록 및 유저의 토탈 포인트 갱신
			"point_change": 500,
			"reason": "미션 완료 보상",
			"total_point": 10500
		}
	}
```

[잘못된 요청] ⇒ 잘못된 사장님 구분 번호 기입 ⇒ 400 에러

```json
HTTP/1.1 400 Bad Request
{
  "success": false,
  "code": "E400",
  "message": "인증번호가 올바르지 않습니다.",
  "data": null
}
```

[이미 완료된 미션] ⇒ 409 중복 에러

```json
HTTP/1.1 409 Conflict
{
  "success": false,
  "code": "E409",
  "message": "이미 완료된 미션입니다.",
  "data": {
    "diner_mission_id": 101,
    "clear_status": "COMPLETED",
    "clear_at": "2025-10-01-시분밀리초"
  }
}
```

[만료된 미션] ⇒ 410 GONE 에러

```json
HTTP/1.1 410 Gone
{
  "success": false,
  "code": "E410",
  "message": "해당 미션은 만료되었습니다.",
  "data": {
    "diner_mission_id": 101,
    "expire_date": "2025-09-30"
  }
}
```

[ 401 인증 에러와 500 서버 에러]

⇒ 1번과 동일

### **회원 가입 하기(소셜 로그인 고려 X)**

![스크린샷 2025-09-30 오후 11.32.45.png](attachment:99ae16e7-a9bf-4336-beb5-afcaee2ce847:스크린샷_2025-09-30_오후_11.32.45.png)

- **API Endpoint + (Path variable / query String )**
  **Path variable**
  ```sql
  없음
  ```
  **query String**
  ```sql
  없음
  ```

⇒ login이나 logout, 토큰 재발급, 회원가입등은 /auth 엔드 포인트로 묶는다고 생각했다.

⇒ /users ⇒ 이거는 회원정보 조회나 수정 이런것들로 쓸 예정.

```sql
POST /api/v1/auth/register
```

- **Request Header**

⇒ 회원가입이므로 Access Token은 동봉 안한다.

```json
Host: api.umc.com                          // API 서버 도메인
Content-Type: application/json             // 요청 본문 타입 → JSON
Accept: application/json                   // 응답을 JSON으로 받고 싶다는 의사 표시
```

- **Request Body**

```json
{
  "email": "asdf@email.com",
  "id": "asdf1234",
  "pw": "12345678",
  "name": "기무진",
  "gender": "MAN",
  "birth": "2001-03-20",
  "address": "인하대 근처 어딘가",
  "food_type": [
    // 중복 선택 가능이니까 => 배열로 전달
    "한식",
    "중식",
    "일식"
  ]
}
```

---

- **Response**

[성공]

⇒ 회원가입시 바로 자동으로 로그인이 될 수 있도록, 토큰을 주기로했다.

```json
HTTP/1.1 201 Created

{
	"success": true,
	"code": "S201",
	"message": "회원가입이 완료되었습니다."
	"data": {
		"user": { // 프론트가 프로필 조회 API 요청 한번 더 안해도 되게, 프로필 띄울 데이터도 같이 던져줌.
			"id": 15,
			"email": "asdf@email.com",
			"name": "기무진",
			"total_point": 0
		},
		"token": { // 바로 로그인 되게 토큰 바로 줌.ㄴ
			"access_token": "asdasdsad124912i39...",
			"refresh_token": "asdad1j2ijei12jri89..."
		}
	}
}
```

[잘못된 요청] ⇒ 400 에러 ⇒ 비번 최소 길이나 이메일 형식 안지켰을 경우나 필수값 누락시 에러.

```json
HTTP/1.1 400 Bad Request
{
  "success": false,
  "code": "E400",
  "message": "잘못된 요청입니다. 이메일 형식이 올바르지 않습니다.",
  "data": null
}
```

[중복 회원] ⇒ 이미 있는 이메일 ⇒ 409 에러 처리 (409라는 중복 전용 상태 코드 있는거 처음 알았다)

```json
HTTP/1.1 409 Conflict
{
  "success": false,
  "code": "E409",
  "message": "이미 가입된 이메일입니다.",
  "data": null
}
```

[401 인증인가 에러 / 500 서버 에러]

⇒ 이것들 외에도 예외 처리 더 할거 있는지 궁금
