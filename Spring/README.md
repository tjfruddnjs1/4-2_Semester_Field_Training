# Spring(MilkTv2)

## 3주차 : 스프링 개발 환경 세팅 및 폴더 구조화, application-local.yml

> MilkT 라는 프로젝트의 2번째 버전을 만들게 되었는데 먼저 Spring Framework를 많이 이용해본적이 없어 기존 프로젝트를 분석하고 주로 사용하게될 모듈인 querydsl을 먼저 2주동안 공부할 수 있는 시간을 부여받았습니다.

```
ITZ-ADMIN
|   .idea
|   .mvn
|   src
    | main
        | java
            | ...
                | config
                | controller
                | domain
                | dto
                | exception
                | intercepter
                | constant
                | repository
                    | custom
                        | Custom ~ respository interface
                    | support
                        | ~ repositoryImpl.class
                    ~ repository.class
                | service
                | util
                ServeletInitializer
                itzAdminApplication
        | resources
            | static
                | css
                | fonts
                | js
                | ...
            | templates
                | chat
                | customer
                | fragments
                    |   footer.html
                    |   header.html
                    |   ...
                | index.html
                | ...
        | application.yml
        | application-local.yml
        | data.sql
        | ...
```

- 스프링 개발 환경 세팅 및 폴더 구조는 `4-1 하기계절제 현장실습` 당시 진행했던 내용과 겹치는 부분이 많아 생략하겠습니다
- 당시 공부하면서 정리했던 내용들이 겹치는 부분이 많기 때문에 세팅하는 작업 및 각 폴더에 대한 설명은 아래 링크 설명으로 대체하고 `application-local.yml` 파일 생성과 해당 파일에 대한 설정 방법 및 필요 이유, `repository 폴더 내의 custom, support 폴더`에 대해 설명해보겠습니다.

> 링크 : https://github.com/tjfruddnjs1/4-1_Summer_Field_Training/tree/main/JavaSpring

1. application-local.yml

- 개발 환경을 분리 (개발, 운영, 로컬) 시키는 작업
- 하나의 yml 파일 내에서 ---을 통해 구분하는 것도 가능하지만 yml 파일이 커짐에 있어 관리하기 힘들기 때문에 개발자 각 환경에 맞추어 세팅하고 분리하는 것이 합당

```yml
spring:
  datasource:
    url: ~
  profiles:
    active: local
```

- 파일 형식은 위와 같고 사용하고자하는 RDS 주소만 모두 다르게 세팅하여 환경마다 필요한 yml 파일을 불러다가 사용
- 이후 run/debug setting > edit configurations > active profiles 값에 실행 시키고자 하는 profiles 명을 적어준다.

> 프로젝트 진행 당시 개발 이해관계자가 저 포함 총 5명이였고 (1명은 프로젝트를 부탁한 고객 입장) 해당 프로젝트를 실제로 필요로 하는 사람이 존재하였기 때문에 다음과 같이 기능에 따른 개발 환경을 위와 같이 분리하였습니다.

2. repository의 custom, support 폴더

- 위를 설명하기 이전에 JPA, querydsl을 이용해서 데이터베이스에 접근 및 작업 요청을 하게 되었는데 , 이는 domain과 dto 생성시 해당 클래스를 Queydsl에서 인식할 수 있게 도메인 모델인 `QClass` 생성을 필요로 했습니다.
- 이를 위해선 아래의 Maven Tab에서 clean > complie 작업을 필요로 했습니다. 이후, 도메인, DTO가 변경될 때는 complie을 계속해서 해주면서 target의 QClass update를 필요로 했습니다.

<img src="https://user-images.githubusercontent.com/41010744/136921008-aff83e12-e935-44c8-8e05-96b618da5d39.png">

- 이렇게 QClass 까지 생성이 완료되고 나서 실제 쿼리를 동작하는 부분인 repository 의 custom, support 폴더 내에서 querydsl 문법을 이용한 쿼리문을 작성하였습니다.

## 4주차 : querydsl repository 구체화, thymeleaf 사용법

> spring을 공부하게된 2주차로 기존 프로젝트에서 사용하던 회사만의 표현 방식(ex. 변수명, 프로젝트 구조 등)을 어느정도 익히고 실제 view부분을 나타내주는 thymeleaf와 database에서 필요한 정보를 가져올 수 있는 query를 실제로 작성해보았습니다.

> 실제 상세 구현 코드 및 명칭들은 회사 자료이기 때문에 비유적으로 표현했습니다.

1. querydsl repository 구체화

- 먼저 domain에는 소의 상태 및 사용자, 생산량, 사료 성분 등의 농장 내의 생산량 및 탄소 발생량 등을 나타낼 수 있는 table들이 있습니다.
- 해당 table들로 부터 정보를 query를 통해 가져올 수 있도록 domain의 table 수만큼 interface와 실제쿼리를 구현하는 Impl class들을 만들었습니다.

- custom repository 예시 (CustomExampleRepository) :실제 Impl Class에서 구현할 함수를 미리 정의

```java
@NoRepositoryBean
public interface CustomExampleRepository {
  DTO findDayProduction(Integer Id);

  DTO findHistoryByDateRange(Integer Id, LocalDate startDate, LocalDate endDate);

  Map<LocalDate, DTO> findStatusByDateList(List<LocalDate> dateList,
      Integer Id);
}
```

- Implement repository 예시 (CustomExampleRepository) : customRepository에서 정의된 함수를 구현
- `제목은 반드시 인터페이스 이름 + Impl` , `부모의 생성자에 도메인 클래스를 인자`로 넘겨주어야 한다.

```java
public class ExampleImpl extends QuerydslRepositorySupport implements
    CustomExampleRepository {

  QAllInfo allInfo = QAllInfo.allInfo;

  public AllInfoRepositoryImpl() {
    super(AllInfo.class);
  }

  @Override
  public DTO findList(Integer Id){
      return //구현한 query 내용
  };

  @Override
  public GraphCarbonStatusDTO findHistory(Integer Id, LocalDate startDate, LocalDate endDate){
      return //구현한 query 내용
  };

  @Override
  public Map<LocalDate, GraphCarbonStatusDTO> findStatusByDateList(List<LocalDate> dateList,
      Integer Id){
          return //구현한 query 내용
      };
}
```

- repository Interface에서 위의 구현 내용 및 JpaRepository를 extends 하여 해당 database는 service에서 선언하고 controller에서 해당 model을 가공하여 view부분에서 출력
- Repository 예시 (ExampleRepository)

```java
@Repository
public interface ExampleRepository extends JpaRepository<AllInfo, AllCowInfoId>,
    CustomAllInfoRepository {

}
```

2. ThymeLeaf 사용법

- 위에서 구현한 query문을 통해 database내에서 가져온 model을 실제 view부분에 보여주는 부분에 있어 Thymeleaf를 사용해야 했습니다.
- 해당 내용에 관련한 정리는 `하위 폴더로 따로 만들어 정리한 내용을 분류`하였습니다.

## 5주차 : Trigger 학습 및 적용, 오류 해결 정리

> 맡은 프로젝트 내용이 하루에도 무수한 양의 많은 데이터(각 소에 대한 정보 ex. 우유 생산량, 탄소 등)이 들어오기 때문에 해당 Table의 정보가 update 될 때마다 처리해줘야 할 procedure를 필요로 했습니다. 따라서 Trigger에 대해 학습하게 되었고 적용하는 작업을 진행했습니다.

1. Trigger 학습 및 적용

- 이에 해당하는 학습 내용 및 쿼리를 프로그래밍 언어처럼 조건을 사용할 수 있는 PL/SQL에 관련한 학습 내용은 `하위 폴더로 따로 만들어 정리한 내용을 분류`하고 아래에는 실제 적용한 내용을 설명하겠습니다.

- 처음 문제를 마주한곳은 활동 정보가 매일매일 activity 테이블에 누적되고 여러 종류의 date를 가진 id로 부터 누적량 또는 최신 데이터를 가져와야 하는 상황이 필요하여 해당 부분을 테이블에 insert시 trigger시 조건을 판단하여 업데이트 하는 방식을 사용하였습니다.

> 최신 데이터를 가져오는 trigger

```sql
CREATE TRIGGER `latest_activity` AFTER INSERT ON `activity` FOR EACH ROW BEGIN
    DECLARE latest TINYINT(1);

    SELECT EXISTS (
        SELECT * FROM `activity` WHERE `a_id` = NEW.a_id AND `b_id` = NEW.b_id AND `type` = NEW.type AND `activity_date` > NEW.activity_date
    ) INTO `latest`;

    IF `latest` = 0 THEN
        INSERT INTO `latest_activity`(`a_id`, `b_id`, `activity_date`, `type`, `value`)
	    VALUES(NEW.a_id, NEW.b_id, NEW.activity_date, NEW.type, NEW.value) ON DUPLICATE KEY UPDATE `activity_date` = NEW.activity_date, `value` = NEW.value;
    END IF;
END^;
```

> 일별 데이터를 가져오는 trigger

```sql
CREATE TRIGGER `daily_production` AFTER INSERT ON `activity` FOR EACH ROW BEGIN
    DECLARE isExists TINYINT(1);
    DECLARE a_id_sum  INTEGER;

    SELECT EXISTS (
      SELECT * FROM `daily_production`
      WHERE `product_year` = DATE_FORMAT(NEW.activity_date , "%Y") AND `product_month` = DATE_FORMAT(NEW.activity_date , "%m") AND `product_day` = DATE_FORMAT(NEW.activity_date , "%d") AND `b_id` = NEW.b_id
   ) INTO `isExists`;

   IF `isExists` = 0 THEN
      INSERT INTO `daily_production`(`b_id`, `product_year`, `product_month`, `product_day` , `total_value`,`a_id_sum`)
      VALUES(NEW.farm_id, DATE_FORMAT(NEW.activity_date , "%Y"), DATE_FORMAT(NEW.activity_date , "%m"), DATE_FORMAT(NEW.activity_date , "%d"), NEW.value, 1);
   ELSE
    SELECT `a_count`
    INTO `a_sum`
    FROM `daily_production`
    WHERE `product_year` = DATE_FORMAT(NEW.activity_date, "%Y") AND `product_month` = DATE_FORMAT(NEW.activity_date, "%m") AND `product_day` = DATE_FORMAT(NEW.activity_date, "%d") AND `b_id` = NEW.b_id;

    UPDATE `daily_production` SET `total_value` = `total_value` + NEW.value, `a_count` = `a_sum` + 1
    WHERE `product_year` = DATE_FORMAT(NEW.activity_date , "%Y") AND `product_month` = DATE_FORMAT(NEW.activity_date , "%m") AND `product_day` = DATE_FORMAT(NEW.activity_date , "%d") AND `b_id` = NEW.b_id;
   END IF;
```

- 하지만 위의 방식으로 트리거를 생성하고 나니 수많은 데이터를 연산하는데 있어 두 번씩 필요한 데이터가 비교 및 수정/삽입 연산이 많이 일어나게 되어 DBMS에 부하를 일으켜 연산 속도가 많은 차이가 일어났습니다.
- 해당 관련해서는 이후 연산 시간을 줄이기 위한 다른 방법을 찾거나 trigger 문을 보완할 예정입니다.
- 현재 상태에서는 7주차까지 프로토타입을 완성해야 했기에는 해당 trigger로 부터 만들어진 table로 작업을 진행하였습니다.

2. QueryDSL 관련 오류 해결 정리

> Intellij에서 QueryDSL 자동생성 클래스 찾지 못하는 경우

- File > Project structure

<img src="https://user-images.githubusercontent.com/41010744/132791357-48b8e545-7d67-4e93-8fc5-87397a10139e.png">

- `Modules` 탭에서 `target/generated-sources/java` 폴더 클릭한 뒤 상단 `Mark as`의 `Sources` 클릭하여 소스폴더로 인식하도록 설정

<img src="https://user-images.githubusercontent.com/41010744/132791449-125e8481-87d9-4ffe-a3e8-3d8c4f901440.png">

> 스프링 부트 2.5 업데이트 : hibernate, data.sql 관련 변동사항

- data.sql 스크립트를 초기화하는 과정 중간에 테이블을 찾지 못하는 상황
- spring boot : 2.4 > 2.5 버전 변동하는 데 있어 아래와 같은 변동 사항

```html
By default, data.sql scripts are now run before Hibernate is initialized. This
aligns the behavior of basic script-based initialization with that of Flyway and
Liquibase. If you want to use data.sql to populate a schema created by
Hibernate, set spring.jpa.defer-datasource-initialization to true. While mixing
database initialization technologies is not recommended, this will also allow
you to use a schema.sql script to build upon a Hibernate-created schema before
it’s populated via data.sql.
```

- spring boot 2.5 버전 부터 스크립트 기반 초기화의 동작 과정을 flyway, liquibase와 일치 시키기 위해
- data.sql은 hibernate 초기화되기 전에 실행된다는 내용
- 따라서 hibernate 초기화를 통해 생성된 스키마에다가 데이터를 채우기를 위해서 `data.sql`가 실행되기를 원한다면 application.yml(도는 properties)에 **spring.jpa.defer-datasource-initialization 옵션 값을 true**로 추가

## 6주차 : query 작성을 위한 sql문 학습 및 querydsl으로의 변환

> database table들 로부터 실제 쿼리를 작성하기 전 많이 사용하지 않았던 query 개념들을 익히는 작업을 먼저 진행했고 테이블에 쿼리문을 sql로 작성하고 해당 sql로부터의 결과를 querydsl 문법으로 옮기는 작업을 수행했습니다.

> 기본적인 CRUD 작업은 많이 작업해보았기 때문에 이외의 내용에 대해 다루어 보겠습니다.

1. query 작성을 위한 sql문 학습

- OrderBy(+ Having 절 포함) : `Spring/OrderBy`
- GroupBy(+ 집계 함수 포함) : `Spring/GroupBy`
- Join : `Spring/Join`
- SubQuery : `Spring/SubQuery`

- (위 경로의 폴더내에 정리했습니다.)

2. querydsl로의 변환

- query문 이전에 `where절에 조건`으로 들어갈 내용이 `BooleanBuilder` 라는 querydsl api를 통해 만들어지는데 이는 디자인 패턴의 빌더 패턴으로 객체를 생성 하여 생성과정을 따로 분리하여 객체 생성 과정을 제어하기 위한 표현이다.
- **Builder 패턴** : 위의 BooleanBuilder를 통해 메소드 체이닝 방식의 빌더 패턴을 통해 Validation과 불변 객체를 만들수 있어 이 후 유지보수 및 관리에 있어 유용
- **프로젝션 타입** : select 절에 들어가는 대상이 하나 일때와 두 개 이상일 때의 반환 타입이 다르게 되는데 전자는 엔티티의 타입이 되고, 후자는 Tuple 타입이 되어 Tuple 타입시 get() method를 통해 조회하게 된다. 따라서, 프로젝트에서는 DTO을 선언하고 이를 하나 처럼 받아와 method chaining 방식으로 불러오게 된다.
- **return** :
- fetch : 조회 대상이 여러건일 경우. 컬렉션 반환
- fetchOne : 조회 대상이 1건일 경우(1건 이상일 경우 에러). generic에 지정한 타입으로 반환
- fetchFirst : 조회 대상이 1건이든 1건 이상이든 무조건 1건만 반환. 내부에 보면 return limit(1).fetchOne() 으로 되어있음
- fetchCount : 개수 조회. long 타입 반환
- fetchResults : 조회한 리스트 + 전체 개수를 포함한 QueryResults 반환. count 쿼리가 추가로 실행된다.
- **transform** : 해당 인터페이스 주요 구현체는 GroupBy Class 이는 부모 자식 관게에 대한 집합 연산을 지원하여 해당 groupBy에 묶인 컬럼과의 Tuple을 자동 매칭

> 구현 쿼리 예시 1) 각 데이터의 최근 record만을 각 user로부터 불러오기

- https://stackoverflow.com/questions/2411559/how-do-i-query-sql-for-a-latest-record-date-for-each-user
- 해당 구현 쿼리는 위의 링크로부터 같은 테이블을 join하여 sql로 구현하고 이를 querydsl 공식 doc을 참조하여 구현하였습니다.

> 구현 쿼리 예시 2) date(월, 주, 일)를 그룹화하고 오름차순으로 정렬된 필요 컬럼들의 합, 평균을 return 하는 쿼리 > (내 id vs 타 id) data 구분 가능

- 이는 Month, Week, Day를 필요 부분에 출력하기 위해 viewType이라는 `custom enum class`를 생성하고 path 경로를 받아올 수 있는 `NumberPath` 타입을 사용하여 동적으로 받아온 type에 따라 동적으로 가져올 수 있게 작성되었습니다.
- 또한 아래에 excludeMyId 의 boolean type의 변수를 통해 내 id가 아닌 정보를 불러올 수도 있는데 이는 해당 method가 특수한 경우에서만 사용할 수 있도록 제한`(특수성)`하게 되어 `코드 리뷰`를 통해 `(범용성)`있게 이후 변경될 예정입니다.

```java
 @Override
  public Map<Integer, DTO> ComparativeAnalysis(
      @NonNull Integer Id,
      boolean excludeMyId,
      ViewType viewType) {
    BooleanBuilder builder = new BooleanBuilder();
    LocalDate now = LocalDate.now();

    if (excludeMyId) {
      builder.and(allInfo.id.eq(Id));   //equal
    } else {
      builder.and(allInfo.id.ne(Id));   //not Equal
    }

    NumberPath<Integer> group = null;

    switch (viewType) {
      case MONTH:
        group = allInfo.month;
        builder.and(allInfo.date.between(now.minusMonths(Constant.RECENT_MONTH_COUNT), now));
        break;
      case WEEK:
        group = allInfo.week;
        builder.and(allInfo.date.between(now.minusWeeks(Constant.RECENT_WEEK_COUNT), now));
        break;
      case DAY:
        group = allInfo.day;
        builder.and(allInfo.date.between(now.minusDays(Constant.RECENT_DAY_COUNT - 1), now));
        break;
    }

    return from(allInfo).where(builder)
        .orderBy(allInfo.date.asc())
        .groupBy(group)
        .transform(GroupBy.groupBy(group)
            .as(new DTO(allInfo.ratioAvg.avg(),
                allCowInfo.Feed.sum())));
  }
```

> 구현 쿼리 예시 ... (생략)...

## 7주차 : 작성된 query를 바탕으로 prototype 결과물 구현, 리팩토링

> 위에서 작성한 query 결과 및 thymeleaf 문법, chart.js api로부터 비교 graph 출력 및 협업하는데 있어 중복되는 부분 수정 하는 주차가 되었습니다. 구현된 페이지는 총 4개였지만 제가 구현한 2개 부분만 결과로 넣었습니다.

- 결과물은 아래의 prototype 구현 결과로 넣었고, 리팩토링에 있어서는 협업을 하는데 있어 중복되는 DTO나 table 같은 부분 및 로직 상의 공통되는 부분을 합치고 변수 명을 통일하는데에 있어 협업하는 분과의 조율을 하였습니다.

## prototype 구현 결과 : 차례대로 농장 간의 상태 비교, 월/주/일 간의 상태 비교

<table>
<tr>
<td><img src="https://user-images.githubusercontent.com/41010744/137102091-1ec74a05-9c1e-4c2f-a73c-b2ab717274f8.png"></td>
<td><img src="https://user-images.githubusercontent.com/41010744/137102336-d3e9fff1-e5be-4cfa-a11d-7235b9286820.png"></td>
<td><img src="https://user-images.githubusercontent.com/41010744/137102451-7f6e2db4-83ed-407a-9033-ab4bc1f68153.png"></td>
<td><img src="https://user-images.githubusercontent.com/41010744/137102542-bbb465a7-cc99-42d9-9137-b05ed2576899.png"></td>
</tr>
</table>
