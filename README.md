# 2021 2학기계절제 현장실습 (회사명 : UserInsight)

## 목차

1. [React](<#React(ITZ-WEB)-1주차-~-2주차>)
2. [Spring](<#Spring(MilkTv2)-3주차-~-15주차>)
3. [Test](<#Test-13주차>)

### React(ITZ-WEB) 1주차 ~ 2주차

> 개요 :

- 이전 1학기 하기계절제에 진행했던 프로젝트의 추가 기능 및 개선사항을 리팩토링하기 위해 2주차까지는 진행하던 프로젝트에서 해당 기능을 수행하였습니다.
- 환경 세팅 관련 내용은 하기계절제에서 진행했던 점과 동일하므로 생략하겠습니다.

> 관련 자세한 내용은 React 폴더내 정리하였습니다.

### Spring(MilkTv2) 3주차 ~ 15주차

> 개요 :

- React(ITZ-WEB)에서는 NoSQL의 firebase를 이용하여 실제 쿼리문을 작성해본 경험이 부족한 것 같아 회사에서 쿼리문을 많이 사용할 수 있는 RDBMS 프로젝트 (Spring querydsl 이용)를 부여받았습니다.

> 환경 세팅

1. Pom.xml

- spring-boot-starter package : web, tomcat, test, thymeleaf, JPA, dialect, security, validation
- lombok
- JPAAnotation, `querydsl` : database을 query문을 통해 작업할 수 있는 가장 핵심 모듈
- maven-plugin

2. Database

- MariaDB
- heidiSQL

3. IDE, JDK

- Maria DB : 10.3(x64)
- heidiSQL : database 내부 테이블 구조 및 데이터들을 ui로 구조화 하여 보여주는 프로그램 (mysql workbench 와 유사)

4. 협업 도구

- Git
- Yona ITS : git commit & push 시 코드 변경 내용을 확인 및 리뷰가 가능하며 이슈 담당자를 지정해 해당 이슈를 commit 내용에서 언급 시 자동으로 mapping 해주는 편리한 협업을 위한 도구

> 관련 자세한 내용은 Spring 폴더내 정리하였습니다.

### Test 13주차
