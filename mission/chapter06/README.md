> **GitHub 저장소 주소**
>
> https://github.com/Jinacker/UMC_9th_Node_Practice/tree/feature/chapter06

---

1. 기존에 구현했던 API의 Repository 함수들을 모두 Prisma ORM을 이용하도록 변경해주세요.

- 미션 수행

1. 미션 도전 API 리팩토링

POST /api/v1/user-missions/1/challenge

![스크린샷 2025-11-09 오후 4.10.26.png](attachment:c36cb4b8-ade6-4623-8c5e-b2d20d62b7c0:스크린샷_2025-11-09_오후_4.10.26.png)

![스크린샷 2025-11-09 오후 4.10.17.png](attachment:74813a81-5aa7-4e3f-b9cf-e3efefe23b2f:스크린샷_2025-11-09_오후_4.10.17.png)

---

1. 식당 등록 API

![스크린샷 2025-11-09 오후 4.43.42.png](attachment:9cc97d3f-0be9-4b02-ac3b-0f1c6cda1ad6:스크린샷_2025-11-09_오후_4.43.42.png)

---

1. 가게 미션 등록 API

![스크린샷 2025-11-09 오후 4.53.26.png](attachment:df03b0dc-ff12-4763-b674-635134e60b1a:스크린샷_2025-11-09_오후_4.53.26.png)

---

1. 리뷰 작성하는 API

![스크린샷 2025-11-09 오후 5.26.42.png](attachment:5a81c879-0d78-46bc-8f8b-f9dec7b51a6a:스크린샷_2025-11-09_오후_5.26.42.png)

2. 내가 작성한 리뷰 목록

- 참고 화면

![Untitled](attachment:fc5de1f6-d216-4dbc-9fa6-10b30e8ee348:9864d338-162d-42d6-9605-6f5ca4795c84.png)

- 미션 수행 및 PostMan 결과

이번 실습에서 배운 커서 페이지네이션도 함께 구현해봤다.

Prisma ORM에서는 성공 or null 이라는것도 처음 알았고,

npx prisma studio ⇒ 이거 꽤나 좋다. DB 켜기 귀찮을때 최고인듯하다.

아직 타입 스크립트가 익숙하진 않아서 계속 타입 에러가 많이 뜬다…

async 비동기 함수에선 리턴 타입이 Promise<타입> 인걸 알게되었다. 비동기 함수의 리턴값이라는걸 알려주는 용도로도 볼 수 있다.

```jsx
Repository에서 받아온 배열은 map으로 각각 DTO에 파싱했다.
return myReviews.map(responseFromReview); // Repository에서 받아온 리뷰 배열 => DTO에 map으로 각각 파싱
```

![스크린샷 2025-11-05 오전 1.03.59.png](attachment:bb017fee-d69a-4695-82f2-2a6c1108ffb6:스크린샷_2025-11-05_오전_1.03.59.png)

## ![스크린샷 2025-11-05 오전 1.05.30.png](attachment:0d0acaa0-258b-4547-90c8-53bf0a442c8c:스크린샷_2025-11-05_오전_1.05.30.png)

1. 특정 가게의 미션 목록
   - 미션 수행
     ![스크린샷 2025-11-05 오후 12.41.56.png](attachment:baf9a1e0-55e2-4671-8404-d8a1ca7100f7:스크린샷_2025-11-05_오후_12.41.56.png)
     Prisma studio에서 레코드 넣다보면 ⇒ 가끔 이렇게 뜨는데 뭐가 문제인지 궁금하다. 화면을 넘어가서 클릭도 안됨. 근데 datagrip에선 insert 잘된다.
     ![스크린샷 2025-11-05 오후 12.52.06.png](attachment:01614eca-3041-48c3-bd30-d4ac53fcaa86:스크린샷_2025-11-05_오후_12.52.06.png)
     요런식으로 프리즈마 스키마에 적용한 컬럼명 보면서 하는데, 좀 더 편리하게 자동으로 불러오거나 하는 방법이 있는지 궁금하다.
     그리고 계속 궁금했던점 ⇒ types / dto / controller / service / repository 이런거 어떤 순서로 작업하는지 궁금.
     그리고 타입을 어느정도 만들어야하는지도 궁금. ⇒ 타입이 너무 꼬여서 고통스럽다…
     ![스크린샷 2025-11-05 오후 2.52.29.png](attachment:8e43f0c5-71c7-42a6-9e90-79387017d544:스크린샷_2025-11-05_오후_2.52.29.png)
     스키마가 nullable인지도 알아낸다음 ⇒ 맞춰서 | null 해줘야 에러가 사라진다. 이것도 팁 있는지 궁금..
     ![스크린샷 2025-11-05 오후 3.09.52.png](attachment:6d7b5dc8-96f5-4109-a841-775de06b4827:스크린샷_2025-11-05_오후_3.09.52.png)
     와일드 카드로 \* import 했는데 계속 DinerMissionListResponseDTO 타입을 찾을 수 없다고 떠서 ⇒ 따로 import 해줌. ⇒ 왜이러는지 궁금… 딴건 안이랬는데
   - Postman 결과
     ![스크린샷 2025-11-05 오후 3.21.23.png](attachment:110229e0-0e04-4b93-a143-30c329e17f60:스크린샷_2025-11-05_오후_3.21.23.png)
     ![스크린샷 2025-11-05 오후 3.26.10.png](attachment:c329f898-6c42-402b-b30d-dc222b9519b8:스크린샷_2025-11-05_오후_3.26.10.png)
     ![스크린샷 2025-11-05 오후 3.21.00.png](attachment:6d9d290a-d233-464f-a884-71eb089d67f3:스크린샷_2025-11-05_오후_3.21.00.png)
     ⇒ 해당 가게의 미션을 조회하는 기능과 커서 페이지네이션과 없는 가게 예외처리 또한 잘작동한다.
     ⇒ nested된 결과보단 flat하게 펴주는게 가독성이 좋을것 같아 그렇게 했다.
     ⇒ 가게 정보도 같이 주면 좋겠다 싶어서 넣어봤는데, 각 미션마다 나올 필요는 없는것 같아서 빼는게 좋을듯 하다.

---

1. 내가 진행 중인 미션 목록
   - 미션 수행
     ![스크린샷 2025-11-06 오후 2.00.27.png](attachment:af7b00d9-0bfd-491e-9b78-332841c1af28:스크린샷_2025-11-06_오후_2.00.27.png)
     성공 ⇒ 가게랑 미션 내용도 나오게 flat하게 해서 줬다.

---

1. 내가 진행 중인 미션을 진행 완료로 바꾸기
   - 참고 화면
     ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f1912130-0409-4e90-a90f-6091ae253e73/6183f6c0-4a41-4425-93dd-de8a20c5e3ad/Untitled.png)
   - 미션 수행
     잘 바뀐다 ⇒ 수행 완료 Status와 endDate도 같이 업데이트 해준다.
     ![스크린샷 2025-11-06 오후 6.13.02.png](attachment:cb826598-d943-4e9b-9fe7-6a0d5ce665a8:스크린샷_2025-11-06_오후_6.13.02.png)
     ⇒ 성공으로 바뀌는것으로만 끝이 아니라 이거랑 연계된 유저의 pointhistory 내역 생성 + 유저 테이블 포인트 추가 해줘야되는데 이거는 트랜잭션으로 마저 더 구현해봐야겠다.

---

- **try catch는 service에서는 안쓴다? ⇒ 대신 throw로 컨트롤러로 올려주기**
  ### **Controller**
  - 요청/응답의 **최종 책임자**
  - 에러가 여기까지 오면 **클라이언트에게 응답을 보내야 함**
  - 그래서 **try–catch 필수**
    → 에러를 잡아서 `next(error)`로 넘기기 위함
  ***
  ### **Service**
  - **비즈니스 로직 계층**
  - 에러를 여기서 잡으면 **DB 오류인지, 검증 오류인지 상위가 구분 못함**
  - 즉, 서비스에서 try–catch를 쓰면 **에러를 숨기게 되어 유지보수 안 좋아짐**
  - 그래서 **기본적으로 try–catch를 쓰지 말고 throw만 올리기**
  ***
  ### **Repository**
  - DB와 직접 통신 → **DB 오류(PK/FK/unique 등)가 많이 발생하는 레이어**
  - DB native error를 그대로 서비스까지 올리면 메시지가 너무 낮은 수준(DB 내부 코드)
  - 그래서 **필요할 때만 try–catch로 DB error → 도메인 에러로 변환**
    → 예: “이 이메일은 이미 존재합니다” 같은 비즈니스 에러로 바꿔주기
  ***
  # ✅ 최종 정리
  - **Controller:** try–catch 사용 (에러 최종 처리)
  - **Service:** try–catch X (에러 숨기지 말고 그대로 throw)
  - **Repository:** DB 오류 변환이 필요할 때만 try–catch
    이게 가장 깔끔하고 유지보수 좋은 구조임.

**⇒ 어떤 순서로 개발하는지 궁금**

**⇒ 타입 이거는 맨 마지막에 하고 적용하는게 나은가… return 타입 설정하면서 에러가 너무난다..**

- 1. Prisma Client 네이밍 규칙 (Repository에서 테이블명이 바뀌는거)
     Prisma의 모델명은 **PascalCase**로 쓰지만
     Prisma Client에서 생성되는 객체는 **camelCase**로 자동 변환된다.
     즉,
     | schema.prisma 모델명 | Prisma Client에서 사용하는 이름 |
     | -------------------- | ------------------------------- |
     | User | prisma.user |
     | Mission | prisma.mission |
     | MissionClearLog | prisma.missionClearLog |
     | DinerMission | prisma.dinerMission |
     | PointHistory | prisma.pointHistory |
     이게 Prisma의 공식 규칙임.
