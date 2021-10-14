# SubQuery

## JPAExpressions

- select절과 where절에서만 지원

> 예시 1. select 절

- 예시로 사용할 DTO

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class MemberCountDTO {

    private String teamName;
    private Long memberCount;
}
```

- select 절에서의 SubQuery : JPAExpressions 객체를 이용하여 서브쿼리 생성
- ExpressionUtils는 QueryDSL 내부에서 새로운 expression 생성하도록 도와줌
- 두 번재 인자로 alias를 지정하여 객체 주입

```java

    @Test
    public void simpleSubQueryTest() {
        QMember m = QMember.member;
        QTeam t = QTeam.team;

        query.select(Projections.fields(MemberCountDTO.class, t.teamName,
                ExpressionUtils.as(
                        JPAExpressions.select(m.team.count())
                                .from(m)
                                .where(m.team.eq(t)),
                        "memberCount")
        ))
                .from(t)
                .fetch()
                .stream()
                .forEach(result -> {
                    log.info("team name is : " + result.getTeamName());
                    log.info("member count is : " + result.getMemberCount());
                });
    }
```
