# Trigger

- 특정 테이블에 DML문이 수행되었을 때, 데이터베이스에서 자동으로 동작하도록 작성된 프로그램
- 즉, 사용자가 직접 호출하는 것이 아니라, 데이터베이스에서 자동적으로 호출하는 것이 가장 큰 특징
- 트리거는 테이블과 뷰 데이터베이스 작업을 대상으로 정의할 수 있으며, 전체 트랜잭션 작업에 대해 발생되는 트리거와 각 행에 발생되는 트리거가 존재

```sql
CREATE TRIGGER 트리거명 [ BEFORE | AFTER | INSTEAD OF] { DELETE | UPDATE [OF 컬럼명, ...] | INSERT }
ON 테이블명 [ FOR EACH ROW | FOR EACH STATEMENT ] [ WHERE 조건식 ]
 BEGIN SQL문1; SQL문2; ...
 END;
```

> vs procedure
> <img src="https://user-images.githubusercontent.com/41010744/134627403-a7d7d0cf-4360-4ff7-9a76-266623e7c130.png">

## 트리거 적용 예시

> 어떤 쇼핑몰에 하루에 수만 건의 주문이 들어옵니다. 주문데이터는 주문일자, 주문상품, 수량, 가격이 있으며, 수천명의 임직원이 일자별, 상품별 총 판매수량과 총 판매가격으로 구성된 주문 실적을 실시간으로 온라인상에 조회를 했을 때, 한사람의 임직원이 조회할 때마다 수만 건의 데이터를 읽고 계산해야합니다. 만약 임직원이 수만명이고, 데이터가 수백만건이라면, 또 거의 동시다발적으로 실시간 조회가 요청된다면 시스템 퍼포먼스가 떨어질 것입니다.

- 따라서 트리거(Trigger)를 사용하여 주문한 건이 입력될 때마다, 일자별 상품별로 판매수량과 판매금액을 집계하여 집계자료를 보관하도록 만들어보겠습니다. 먼저 관련된 테이블을 생성

<img src="https://user-images.githubusercontent.com/41010744/134627920-79242e93-f244-4c86-8d4e-48c883735a84.png">

- 주문정보테이블에 실시간으로 데이터가 입력될 때마다 트리거가 발동되어 자동으로 일자별판매집계테이블에 일자별, 상품별 판매수량과 판매금액을 계산해 업데이트 하는 작업을 하도록 하고, 사용자들은 미리 계산된 일자별판매집계테이블을 조회하게 하여 실시간 조회를 지원

## 트리거 구현

> 예시 data

```sql
CREATE TABLE ORDER_LIST(
    ORDER_DATE  CHAR(8) NOT NULL,
    PRODUCT     VARCHAR2(10) NOT NULL,
    QTY         NUMBER NOT NULL,
    AMOUNT      NUMBER NOT NULL

);

CREATE TABLE SALES_PER_DATE(
    SALE_DATE   CHAR(8) NOT NULL,
    PRODUCT     VARCHAR2(10) NOT NULL,
    QTY         NUMBER NOT NULL,
    AMOUNT      NUMBER NOT NULL
);
```

> trigger

<img src="https://user-images.githubusercontent.com/41010744/134629156-87ce5aee-77eb-476c-b4c5-6940aaf21dcf.png">

1. 8~14 줄 : trigger 선언

- order_list 테이블에 insert 발생 시 each row 즉 각 행에 해당 트리거를 적용한다라는 뜻
- 또한 declare 선언문에는 변수 선언
- order_list 테이블에 있는 order_date, product Type에 맞게 o_date, o_prod 변수 선언

2. 15~17 줄

- new는 트리거에서 사용하는 구조체 > 새로 입력된 레코드 값을 담고 있습니다.
- o_date에 새로 들어온 order_date 값을, o_prod에 새로 들어온 product 값을 저장

3. 18~26 줄

- sales_per_date 테이블에 update 구문을 실행하여 기존에 잇는 qty.amount를 누적합하여 다시 set
- 여기서 where문을 통해 현재 새로 들어온 날짜와 상품이 일치하는 데이터만 해당 update문을 실행하도록 조건을 건다.
- 만일 해당 조건에 모두 해당되지 않는다면, if sql%notfound 구문이 실행 > 기존에 있던 레코드 값이 아니고 전혀 새로운 레코드이기 때문에 insert 구문을 통해 새로 들어온 데이터를 새로 삽입

> 이제 order_list 테이블에 레코드를 insert 해서 sales_per_date 테이블에 트리거(Trigger)가 자동으로 동작

## 트리거와 트랜잭션의 상관관계

- 다른 상품으로 주문 데이터를 입력한 후 두 테이블의 결과를 조회해보고 트랜잭션을 ROLLBACK 해보겠습니다.
- 판매 데이터의 입력취소가 일어나면, 주문 정보 테이블(ORDER_LIST)과 판매 집계테이블(SALES_PER_DATE )에 동시에 입력(수정) 취소가 일어나는지 확인해보기 위함

```sql
INSERT INTO ORDER_LIST VALUES('20120901','MULTIPACK',10,300000);
```

<img src="https://user-images.githubusercontent.com/41010744/134630351-de9d2cd2-166a-4705-b592-09836b3d712d.png">

- SALES_PER_DATE 테이블은 트리거에 의해 판매 날짜별, 상품별 누적 물량과 가격이 업데이트

<img src="https://user-images.githubusercontent.com/41010744/134630473-5fa7838b-419b-46b2-8795-596da161d6c4.png">

> ROLLBACK 시 ,order_list , sales_per_date 테이블에도 똑같이 입력 취소

<img src="https://user-images.githubusercontent.com/41010744/134630598-26c269f4-0b75-430d-851d-d0fcd5b007c3.png">

- 즉, 트리거는 database에 의해 자동 호출되지만 결국 insert, update, delete 구문과 하나의 트랜잭션 안에서 일어나는 일련의 작업들

> 추가로 트리거는 begin ~ end 절에서 commit, rollback을 사용 불가능
