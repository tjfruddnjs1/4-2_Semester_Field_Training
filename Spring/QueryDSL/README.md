# QueryDsl

- JPQL(Java Persistence Query Langauage)는 JPA의 일부로 정의된 플랫폼에 독립적인 객체지향 쿼리 언어
- 관계형 데이터베이스의 엔티티에 대한 쿼리를 만드는데 사용
- JPA는 엔티티 객체를 중심으로 개발하므로 SQL 사용하지 않고, 하지만 `검색 쿼리`를 사용할 때는 SQL을 사용
- SQL의 영향을 받아 SQL과 비슷하나, database table에 직접 접근하는 것이 아닌 JPA Entity에 동작
- **따라서, JPQL의 쿼리에는 테이블이 아닌 엔티티에서 사용되는 컬럼의 이름을 사용**
  > SQL : 데이터베이스 테이블을 대상으로 쿼리
  > JPQL : 엔티티 객체를 대상으로 쿼리

## JPQL로 검색 ?

- JPA에서 SQL을 직접사용하지 않고, Query Method, @Query, Querydsl 등을 사용
- 관계형 데이터베이스에서 각 테이블이 서로 FK로 참조하듯이 엔티티에서도 서로 연관이 있다면 참조 가능
- 만약 단일 엔티티 경우(FK를 갖지 않는) Querydsl로 비교적 간단하게 검색 기능 작성 가능
- 하지만, **여러 엔티티가 있고 서로 외래키로 참조하고 있는 경우 JPQL로 직접 처리하는 것은 Object[] 타입으로 나오기 때문에 (Tuple 형태) 작성하는 방법 자체가 다르고 복잡**

## Querydsl

- 오픈 소스 프로젝트 & type-safe한 쿼리를 위한 Domain Specific Language
- SQL Query는 문자이기 때문에 type-check가 불가하고 실행해 보기 전까지 작동 여부 확인이 어려운데, 만약 SQL이 Class처럼 Type을 갖고 java코드로 개발 가능하게 해주는 프레임워크

> Querydsl => JPQL => SQL

## Repository 확장

- Spring Data JPA의 Repository를 확장하기 위해서 다음과 같은 단계로 처리

1. 쿼리 메소드나 @Query 등으로 처리할 수 없는 기능은 별도의 인터페이스로 설계
2. 별도의 인터페이스에 대한 구현 클래스를 작성 > `QuerydslRepositorySupport`라는 클래스를 부모 클래스로 사용
3. 구현 클래스에 인터페이스 기능을 Q도메인 클래스와 JPQLQuery를 이용해서 구현

> QuerydslRepositorySupport

- Querydsl을 이용해 쿼리를 작성하기 위해서는 이 클래스를 상속받아야 하며 super(DslMemeber.class); 처럼 도메인 엔티티 클래스를 슈퍼타입인 QuerydslRepositorySupport 생성자의 인자로 넘겨주어야 한다.
- 이는, Spring Data JPA에 포함된 클래스로 Querydsl 라이브러리를 이용해 직접 무언가 구현할 때 사용

<img src="https://user-images.githubusercontent.com/41010744/135038903-fe85cb14-6f5c-4c88-8824-7ef3bd13aa89.png">

- Repository를 확장시키기 위해 repository 패키지 밑에 별도의 패키지를 만들어 인터페이스와 이를 구현한 클래스를 작성 (ex. cowRepository , cowRepositoryImpl)
- 구현된 클래스에서 가장 중요한 점은 QuerydslRepositorySupport를 상속해야 하고, 생성자가 존재하므로 클래스 내에서 super()를 이용해 호출해야 한다는 점 