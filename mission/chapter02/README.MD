![alt text](<UMC_01 (1).png>)

#### 1주차 과제 ERD -> 피드백 적용 버전

1. **내가 진행중, 진행 완료한 미션 모아서 보는 쿼리(페이징 포함)**

![Untitled](attachment:fb2c37cf-9eb2-4e18-b21c-af42c1dd2cc0:Untitled.png)

```sql
SELECT
	d.name,
	m.reward,
	m.quest,
	mc.clear_at // 이게 NULL인지 아니면 값이 있는지로 성공 여부 확인 => NULL 이면 진행중
FROM mission_clear_log as mc
JOIN diner_mission as dm ON mc.diner_mission_id = dm.id
JOIN mission as m ON dm.mission_id = m.id
JOIN diner as d ON dm.diner_id = d.id
WHERE mc.user_id = ? // 로그인한 사용자 바인딩
	AND mc.clear_at IS NOT NULL // NULL 아니면 완료된 미션 목록 / IS NULL 이면 진행중인 미션 목록
ORDER BY mc.clear_at DESC; // 최신순으로 정렬
```

커서 페이지네이션? ⇒ 일단 페이지네이션 제외하고 생각해서 쿼리문 짬. ⇒ JOIN을 세번이나 했는데 맞게 한건지 궁금하다. 더 좋은 방식이 있는지?

```sql
SELECT
	d.name,
	m.reward,
	m.quest,
	mc.clear_at
FROM mission_clear_log as mc
JOIN diner_mission as dm ON mc.diner_mission_id = dm.id
JOIN mission as m ON dm.mission_id = m.id
JOIN diner as d ON dm.diner_id = d.id
WHERE mc.user_id = ? // 로그인한 유저 id 바인딩
	AND mc.clear_at IS NOT NULL
	AND mc.clear_at < ? // 이전 페이지 마지막 레코드의 clear_at 값 바인딩-> 이전 페이지보다 오래된 (덜 최신순인) 레코드만 조회
ORDER BY mc.clear_at DESC
LIMIT ? + 1; // 페이지 사이즈 + 1 -> 한 페이지에 몇개 보여주는지 바인딩
// ex) 10개 -> 11개 나오면 다음페이지 존재 / 11개 안나오면 다음 페이지 없음 마지막 페이지
```

⇒ 커서 베이스 페이지네이션 구현

⇒ clear_at 은 밀리초까지 받게 해놔서 따로 복합키 처리는 안했음.

---

**2.리뷰 작성하는 쿼리, \* 사진의 경우는 일단 배제**

![Untitled](attachment:ff64bece-430c-45e4-96fa-de1e40b984db:Untitled.png)

```sql
SELECT
	u.name,
	r.star,
	r.content,
	r.created_at
FROM review as r
JOIN user as u ON r.user_id = u.id
WHERE r.diner_id = ?;
ORDER BY r.created_at DESC;
```

⇒ 리뷰 조회 쿼리

ORDER BY 절을 추가해서 작성일자별로 정렬하는것도 좋다고 한다.

```sql
INSERT INTO review (user_id, diner_id, star, content, created_at)
VALUES (?, ?, ?, ?, NOW());
```

⇒ 리뷰 작성 쿼리

---

1. **홈 화면 쿼리 (현재 선택 된 지역에서 도전이 가능한 미션 목록, 페이징 포함)**

![Untitled](attachment:6ad90e83-6e2e-4f56-8779-82d16c7f7303:Untitled.png)

(궁금한점) 미션카운터랑 미션 쿼리를 두개로 나눠서 하는게 맞는건지 궁금.

```sql
SELECT r.region_name, mc.mission_counter
FROM region_mission_user as mc
JOIN region as r ON mc.region_id = r.id
WHERE mc.user_id = ?; // 로그인한 유저 아이디 바인딩
```

⇒ 맨 위에 미션 카운터 호출 쿼리

```sql
SELECT
	d.diner_name,
	ft.food_type,
	dm.expire_date,
	m.quest,
	m.reward
FROM diner_mission as dm
JOIN diner as d ON dm.diner_id = d.id
JOIN mission as m ON dm.mission_id = m.id
JOIN food_type as ft ON d.food_type_id = ft.id
JOIN region as r ON d.region_id = r.id
WHERE r.id = ? // 현재 선택된 지역 id 바인딩
	AND dm.expire_date >= NOW() // 만료 안된 미션만 보여줌.
ORDER BY dm.expire_date desc;
```

⇒ 마이 미션엔 유저가 선택한 지역에서 현재 수행 가능한 미션 목록 출력하는걸로 해석함. ⇒ 일단 페이징 제외하고 구현.

```sql
SELECT
	d.diner_name,
	ft.food_type,
	dm.expire_date,
	m.quest,
	m.reward,
	dm.id
FROM diner_mission as dm
JOIN diner as d ON dm.diner_id = d.id
JOIN mission as m ON dm.mission_id = m.id
JOIN food_type as ft ON d.food_type_id = ft.id
JOIN region as r ON d.region_id = r.id
WHERE r.id = ? // 현재 선택된 지역 id 바인딩
	AND dm.expire_date >= NOW() // 만료 안된 미션만 보여줌.
	AND (dm.expire_date < ? // 이전 페이지 마지막 레코드의 expire_date
		OR (dm.expire_date = ? AND dm.id < ?)) // 혹시라도 만료일 같으면 -> dm 아이디순
ORDER BY dm.expire_date DESC, dm.id DESC
LIMIT ? + 1;
```

⇒ 커서 페이지 네이션 구현

⇒ expire_date는 날짜만 받기 때문에 → diner_mission의 id 와 함께 복합키 처리해줘서 혹시라도 expire_date가 같으면 id 순으로 정렬되게 했다.

---

1. 마이 페이지 화면 쿼리

![Untitled](attachment:2d736cd4-e3ab-4c55-9b1d-ad818cc22dbf:Untitled.png)

```sql
SELECT
	u.name,
	u.email,
	u.phone_number,
	u.total_point,
	u.profile_url
FROM user as u
WHERE u.id = ?;
```

**?** ⇒ MySQL의 바인딩 변수 ⇒ 여기에 동적으로 로그인한 사용자 id 값을 바인딩 해주면 된다고 한다.

user의 total_point 로직은 point_history에 변동이 생기면 업데이트 되도록 생각했다.

```sql
UPDATE user
SET total_point = (
	SELECT SUM(point_change)
	FROM point_history
	WHERE user_id = ?)
WHERE user.id = ?;
```
