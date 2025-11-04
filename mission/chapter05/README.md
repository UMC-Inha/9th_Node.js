### 🍒 공통 미션

이번 주에는 3주차에서 설계한 API URL들을 기반으로, 추가적인 API를 더 구현해볼게요.

아래 API들은 우선 특정 사용자(ex. DB에 저장된 첫 번째 사용자)로 가정하고 구현해주세요. 3주차에 설계하지 않은 API URL이 있다면, 설계를 복습 할 겸 미리 설계해본 후 진행해주세요!

- [x]  1-1. 특정 지역에 가게 추가하기 API

![image.png](attachment:7553d3db-3066-4de1-b18b-2e06ee189a2e:image.png)

- [x]  **1-2. 가게에 리뷰 추가하기 API**
    - 리뷰를 추가하려는 가게가 존재하는지 검증이 필요합니다.

![image.png](attachment:a804d400-95e8-4bb1-8bc4-3e92393f19c2:image.png)

- [x]  1-3. 가게에 미션 추가하기 API

![image.png](attachment:72fd4566-c14a-459f-831b-1aae183ab196:image.png)

- [x]  **1-4. 가게의 미션을 도전 중인 미션에 추가(미션 도전하기) API**
    - 도전하려는 미션이 이미 도전 중이지는 않은지 검증이 필요합니다.
    - 1-3번 API를 구현하지 않은 경우, 1-4번에서는 DB에 미션 정보를 수동으로 기입한 후 진행해야 합니다.

![image.png](attachment:c92e2f13-d8ae-4f9f-ab62-220534e1ce96:image.png)

- 이후 본인의 깃허브 리포지토리를 만들어 `feature/chapter-05` 브랜치에 올린 후,
**해당 GitHub 링크를 본인 워크북에 포함해오기. (미션 기록란에 링크 제출)**
    
    **❗main 브랜치에 올리지 말 것!** (브랜치 명 꼭 `feature/chapter-05` 아니어도 됨!) **❗**
    
    **❗main 브랜치는 11주차의 `무중단 CI/CD (GitHub Actions + AWS EC2)` 에서 사용 할 예정❗**
    
- [x]  **Controller → Service → Repository → DB로 이어지는 요청 흐름을 정리해 주세요.**
    1. express.Router()를 통해서 각 API 라우팅
    2. Controller에서 서비스에서 구현한 기능 호출
    3. service에서 데이터 중복 검증 후 repository의 메소드 호출
    4. repository에서 쿼리 스트링을 통해서 db 접근
    
- [x]  회원가입 API에 비밀번호 해싱 과정을 추가해 주세요.

### 🧙‍♂️ 시니어 미션

- 시니어 미션에서는 4개의 API를 모두 구현해주세요. (모든 API 구현이 필수)
- 현재는 API에서 오류가 발생하면 HTML로 된 콘텐츠가 내려오는데, 이 부분을 JSON 형태의 콘텐츠가 응답으로 내려가도록 개선해주세요.

![image.png](attachment:83b0a35f-5083-45b6-bfcc-d9fc527446fd:image.png)

시니어 미션 체크리스트

- [x]  1번. 4개의 API 모두 구현하기
- [x]  2번. JSON 형태의 콘텐츠가 응답되도록 개선해주기

```tsx
export class ApiResponse<T> {
  success: boolean;
  code: number;
  message: string;
  data?: T;

  constructor(success: boolean, code: number, message: string, data?: T) {
    this.success = success;
    this.data = data;
    this.message = message;
    this.code = code;
  }

  static success<T>(message: string, data?: T, code = 200): ApiResponse<T> {
    return new ApiResponse(true, code, message, data);
  }

  static error(message: string, code: number): ApiResponse<undefined> {
    return new ApiResponse(false, code, message, undefined);
  }
}
```

[배운것들 리마인드](https://www.notion.so/29fb57f4596b803eb890fb5701debc2e?pvs=21)