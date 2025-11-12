- 한 번에 여러 번의 DB 작업을 연달아 처리할 때, 중간에 처리가 실패했는데 DB에는 중간까지만 값이 반영되어 있으면 문제가 있을 것 같습니다. 이를 방지하는 기술로는 Transaction이 있는데, Prisma를 이용해 Transaction을 관리하는 방법을 찾아 정리해주세요. 워크북의 실습 프로젝트에서도 적용할 수 있다면 적용해주세요.
    
    # 트랜잭션과 배치쿼리
    
    총 3가지 시나리오에 따라 6가지 트랜잭션 처리 기법 제공
    
    - 의존적 쓰기: Nested writes
    - 독립적 쓰기: Bluk 연산, $transaction([]) api
    - 읽기-수정-쓰기: Idempotent APIs, Optimistic concurrency control, Interactive transaction
    
    ## Nested Writes
    
    여러 관련된 레코드를 한 번의 api 호출로 처리하는 방법
    
    ```tsx
    // 사용자와 게시글을 동시에 생성
    const newUser = await prisma.user.create({
      data: {
        email: 'alice@prisma.io',
        posts: {
          create: [
            { title: 'Join the Prisma Discord' },
            { title: 'Follow @prisma on Twitter' }
          ]
        }
      }
    })
    ```
    
    ## Bulk 연산
    
    ```tsx
    // 모든 읽지 않은 이메일을 읽음 처리
    await prisma.email.updateMany({
      where: {
        userId: 10,
        unread: true
      },
      data: {
        unread: false
      }
    })
    ```
    
    같은 타입의 여러 레코드를 한 번에 처리한다.
    
    - createMany()
    - updateMany()
    - deleteMany()
    - createManyAndReturn()
    - updateManyAndReturn()
    
    ## $transaction([])
    
    여러 프리즈마 쿼리를 배열로 묶어 순차적으로 실행한다.
    
    ```tsx
    const [posts, totalPosts] = await prisma.$transaction([
    	prisma.post.findMany({where: {title: {contains: 'prisma'} } }}),
    	prisma.post.count()
    ]);
    ```
    
    ## 대화형 트랜잭션
    
    복잡한 로직을 포함한 트랜잭션 처리가 필요할 때 사용한다.
    
    $transaction() 안에 비동기 콜백 함수가 들어가서 일련의 비즈니스 로직 과정을 처리한다.
    
    ```tsx
    async function transfer(from: string, to: string, amount: number) {
      return await prisma.$transaction(async (tx) => {
        // 1. 송신자 잔액 차감
        const sender = await tx.account.update({
          data: { balance: { decrement: amount } },
          where: { email: from }
        })
    
        // 2. 잔액 검증
        if (sender.balance < 0) {
          throw new Error(`${from}의 잔액이 부족합니다`)
        }
    
        // 3. 수신자 잔액 증가
        const recipient = await tx.account.update({
          data: { balance: { increment: amount } },
          where: { email: to }
        })
    
        return recipient
      })
    }
    ```


- 우리는 흔히 DB 쿼리에서 N+1 문제를 마주할 수 있습니다. 예를 들어, 게시글을 조회하면서 각 게시글에 해당하는 댓글들을 개별적으로 쿼리한다면, 댓글을 조회하는 쿼리가 각 게시글마다 추가로 발생하게 되어 N+1 문제가 발생합니다. 이를 Prisma에서 N+1문제를 해결할 수 있는 방법을 정리해주세요.
    
    ## 1. include - eager loading(즉시 로딩)
    
    ```tsx
    const users = await prisma.user.findMany({
      include: {
        posts: true,
      },
    });
    ```
    
    장점
    
    - 코드 간단
    - Prisma 내부에서 SQL join, in 쿼리로 최적화 수행
    - 대부분의 단순한 N+1 문제는 이걸로 해결 가능
    
    단점
    
    - 항상 데이터를 같이 불러오므로, 불필요한 데이터까지 가져올 수 있음
    - 중첩 관계가 깊을수록 쿼리 복잡도 증가 (JOIN 과다)
    
    ## 2. dataloader - batching & caching
    
    GraphQL 서버나 NestJS 등에서 자주 사용되는 접근법
    
    비슷한 쿼리들을 한 번에 묶어 Batch Query로 처리
    
    ```tsx
    import DataLoader from 'dataloader';
    import { prisma } from '../client';
    
    // 1. 로더 생성
    const postLoader = new DataLoader(async (userIds: string[]) => {
      const posts = await prisma.post.findMany({
        where: { userId: { in: userIds } },
      });
    
      // userId별로 그룹핑
      const postMap = userIds.map(
        (id) => posts.filter((p) => p.userId === id)
      );
      return postMap;
    });
    
    // 2. 서비스/리졸버 내에서 사용
    const users = await prisma.user.findMany();
    const usersWithPosts = await Promise.all(
      users.map(async (u) => ({
        ...u,
        posts: await postLoader.load(u.id),
      }))
    );
    
    ```
    
    장점
    
    - GraphQL과 궁합이 good (@nestjs/graphql)
    - 캐싱 및 배칭으로 동일 쿼리 중복 방지
    
    단점: Prisma 단독 사용 시엔 약간 과한 구조일 수 있음
    
    ## 3. FluentAPI
    
    Prisma 5에서 도입됨
    
    쿼리를 체이닝 방식으로 최적화
    
    ```tsx
    const users = await prisma.user
      .fluent() // Fluent API 시작
      .include('posts')
      .where({ isActive: true })
      .orderBy({ createdAt: 'desc' })
      .take(10)
      .exec(); // 최종 실행
    ```
    
    또는 custom include 로직을 재사용 가능하게 구성
    
    ```tsx
    const withPosts = prisma.$extends({
      model: {
        user: {
          withPosts() {
            return this.findMany({
              include: { posts: true },
            });
          },
        },
      },
    });
    // 사용
    const users = await withPosts.user.withPosts();
    ```
    
    장점
    
    - 중첩 include를 깔끔하게 재사용 가능
    - N+1 해결 로직을 하나의 Fluent Chain으로 관리 가능
    - Service 계층에서 중복 쿼리 방지
    
    단점
    
    - 아직 비교적 새로운 기능
    - 복잡한 관계의 자동 batching은 직접 제어해야 함 (DataLoader만큼 자동화되진 않음)