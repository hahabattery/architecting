---
layout: default
title: QueryDSL
grand_parent: Springs
parent: Spring JPA
nav_order: 4
---

# 용어

### 프로젝션
select절에 들어가는 것을 프로젝션이라는 용어로 부른다.

아래 쿼리는 프로젝션 대상이 하나이다라고 한다.
```
    @Test
    public void simpleProjection() {
        List<String> result = queryFactory
                .select(member.username)
                .from(member)
                .fetch();
```

프로젝션시에는 위에처럼 Projections 키워드를 사용할 때도 있고,

new Dto(... 형식으로 constructor를 이용할 수도 있다. 이 경우에는 해당 Dto에 @QueryProjection 를 생성자에 설정해줘야 한다.



# 연관관계가 없는 join
 예전에는 Querydsl 대신에 Jooq를 사용하는 이유로 Querydsl은 연관 관계(relationship) 없이는 조인을 맺지 못하기 때문
 최근 버전에는 연관 관계 없이 조인이 되는 기능을 지원합니다.
 아래와 같이 join(엔티티).on(키.eq(키)) 로 SQL과 유사한 형태로 작성이 가능하다.
 
 ```
 @Override
    public List<AcademyTeacher> findAllAcademyTeacher() {
        return queryFactory
                .select(Projections.fields(AcademyTeacher.class,
                        academy.name.as("academyName"),
                        teacher.name.as("teacherName")
                ))
                .from(academy)
                .join(teacher).on(academy.id.eq(teacher.academyId))
                .fetch();
    }
 ```
 
 
 # groupBy 에 대해서
https://green-joo.tistory.com/51
 
https://lemontia.tistory.com/929



 
 
 # Querydsl 에서 OneToMany 관계에서 Left Outer Join 이 필요할 경우
 https://jojoldu.tistory.com/342
 
 
 # Multi level aggregations are currently not supported by QueryDSL
 
 https://stackoverflow.com/questions/66366976/querydsl-how-to-return-dto-list
 
 
 # QueryDSL에서 exist 사용하기
 
Querydsl 에서 서브쿼리로 exists 를 사용하는 예제이다.

쿼리 성능상 in 보다는 exists 를 사용하는게 유리하다.

게시글 댓글에 특정 단어가 있는 게시물을 검색하는 쿼리이다.
```sql
 SELECT * 
FROM Board A
WHERE EXISTS (SELECT 1 FROM Comment WHERE no = A.no AND content LIKE '%내용%')
;
```

위 쿼리를 queryDSL로 표현하면 다음과 같다.
```
List<Board> list = 
	query.selectFrom(board)
		.where(JPAExpressions.selectFrom(comment).where(comment.board.eq(board)
        	.and(comment.content.contains("내용"))).exists())
		.fetch();
```

 
 