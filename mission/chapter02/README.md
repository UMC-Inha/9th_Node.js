# mission
## prob_01
```sql
SELECT um.id,
       m.id AS mission_id,
       r.name AS restaurant_name,
       m.content,
       m.price,
       m.point,
       um.status,
       m.deadline
FROM user_mission um
JOIN mission m ON um.mission_id = m.id
JOIN restaurant r ON m.restaurant_id = r.id
WHERE um.user_id = ?
  AND um.status IN ("진행중", "진행완료")
  AND um.id < ?
ORDER BY um.id DESC
LIMIT 15;
```

## prob_02
```sql
insert into comment (
    review_id,
    user_id,
    restaurant_id,
    content,
    created_at
) VALUES (
    ? # 어떤 리뷰에 달린 답글인지
    ? # 사장님 user_id
    ? # 해당 가게 id
    ? # 답글 내용
    NOW()
);
```
## prob_03
```sql
# 윗 부분
select *, count(*) 
	from user
	join user_area on user.id = user_area.user_id
	join area on user_area.area_id = area.id
	left join user_mission on user_mission.user_id = user.id
	where user.id = ? and area.id = ?
	group by user.point, area.name; 

# 아랫 부분
SELECT m.id,
       r.name AS restaurant_name,
       m.content,
       m.price,
       m.point,
       m.deadline
FROM user_mission um
JOIN mission m ON um.mission_id = m.id
JOIN restaurant r ON m.restaurant_id = r.id
WHERE um.user_id = ? # user_id
  AND m.id < ? # 마지막 커서
ORDER BY m.id DESC
LIMIT 15;
```

## prob_04
```sql
select * from user
where user.id = ?
```

# senior mission
![내가 진행중, 진행 완료한 미션 모아서 보는 쿼리(페이징 포함)](attachment:fb2c37cf-9eb2-4e18-b21c-af42c1dd2cc0:Untitled.png)

내가 진행중, 진행 완료한 미션 모아서 보는 쿼리(페이징 포함)

- [x]  미션 1(내가 진행중, 진행 완료한 미션 모아서 보는 쿼리(페이징 포함))에서 정렬 기준을 1순위는 포인트로 2순위는 최신순으로 하여 Cursor기반 페이지네이션을 구현해보세요
    - 설명
        
        ```sql
        SELECT um.id,
               m.id AS mission_id,
               r.name AS restaurant_name,
               m.content,
               m.price,
               m.point,
               um.status,
               m.deadline
        FROM user_mission um
        JOIN mission m ON um.mission_id = m.id
        JOIN restaurant r ON m.restaurant_id = r.id
        WHERE um.user_id = ?
          AND um.status IN ("진행중", "진행완료")
          AND um.point < ? # 포인트 커서 추가!
          AND um.id < ?
        ORDER BY um.id DESC
        LIMIT 15;
        ```
        

- [x]  SQL Injection에 대해 조사하고 어떠할 때 일어나고 어떻게 막을 수 있는 지를 적어주세요
    - 설명
        
        ## 정의
        
        SQL인젝션은 키워드 조사에서도 언급했듯이, 사이버 범죄자가 SQL문의 입력 또는 양식 필드에 SQL 쿼리를 삽입하여 코드의 취약성을 악용하려는 시도이다.
        
        웹 기반 입력 양식에서 사용자가 생성한 SQL문이 데이터베이스를 직접 쿼리할 수 있을 때 주로 발생
        
        ## 유형
        
        대역 내 SQLi: 가장 흔하고 쉬운 공격으로, 일반적인 통신 채널(요청/응답)을 통해 공격과 결과가 동시에 이루어짐. 해커가 질문하면 웹페이지에서 답을 바로 볼 수 있는 상황
        
        - 오류 기반: 일부러 틀린 질문 쿼리 > 에러 메시지 > DB 구조 힌트 파악
        - union 기반: 정상적인 검색 결과에 내가 원하는 데이터를 union 연산자를 사용해 끼워 넣기
        
        추론(블라인드) SQLi: DB의 답을 직접 볼 수 없을 때. 서버 반응 참/거짓 혹은 시간을 보고 정보 추론, 느리지만 직접적인 답이 없는 상황에서도 공격 가능
        
        - 부울 기반: DB에 질문 던지고, 참거짓(정상페이지/에러페이지, 빈페이지)을 반복적으로 관찰해 한 글자씩 데이터 알아냄
        - 시간 기반: 부울 기반과 비슷하지만, 에러페이지같은 것이 없을 경우(모두 정상 페이지로 될 경우), “a라면 응답을 5초정도 늦게 해줘”와 같이 질문 던짐
        
        대역 외 SQLi: 통신 채널이 막혀있거나, 직접적인 응답 얻기 어려울 때 외부 서버를 통해 답을 받아냄
        
        - DB에게 외부 네트워크로 정보를 보내줘 라고 명령
        
        ## Reference
        
        https://www.fortinet.com/kr/resources/cyberglossary/sql-injection
        

- [x]  다양한 JOIN 방법들에 대해 찾아보고, 각 방식에 대해 비교하여 간단히 정리해주세요.
    - 설명
        
        ## inner join (데이터 간의 교집합)
        
        두 데이터에서 조건을 만족하는 데이터 쌍만 가져옴
        
        ```sql
        SELECT u.id, u.name, o.order_date
        FROM users u
        INNER JOIN orders o ON u.id = o.user_id;
        ```
        
        ## left join
        
        왼쪽 테이블의 모든 행에 대응되는 오른쪽 테이블의 행을 왼쪽에 붙이고, 오른쪽에 매칭되는 값이 없으면 Null 반환
        
        ```sql
        SELECT u.id, u.name, o.order_date
        FROM users u
        LEFT JOIN orders o ON u.id = o.user_id;
        ```
        
        ## Right join (주로 사용하진 않음)
        
        ```sql
        SELECT u.id, u.name, o.order_date
        FROM users u
        RIGHT JOIN orders o ON u.id = o.user_id;
        ```
        
        ## full outer join (합집합)
        
        양쪽 테이블의 모든 행을 가져오고, 매칭 안되는것은 null
        
        ```sql
        SELECT u.id, u.name, o.order_date
        FROM users u
        FULL OUTER JOIN orders o ON u.id = o.user_id;
        
        ```
        
        ## cross join (데카르트곱, 모든 쌍 반환)
        
        ```sql
        SELECT u.name, p.product_name
        FROM users u
        CROSS JOIN products p;
        ```
        
        ## self join (자기 자신과 조인)
        
        계층구조 표현할 때 사용
        
        예시) 직원 테이블에서 직원-매니저 관계
        
        ```sql
        SELECT e.name AS employee, m.name AS manager
        FROM employees e
        LEFT JOIN employees m ON e.manager_id = m.id;
        ```
        
        ## natural join
        
        같은 이름을 가진 칼럼 기준으로 자동으로 조인
        
        ![image.png](attachment:c0d0e878-5d45-4c75-aebc-96f69dbce0cb:image.png)
        
        ```sql
        SELECT *
        FROM users
        NATURAL JOIN orders;
        ```
        

정리된 글을 바탕으로 블로그를 작성하여 주세요