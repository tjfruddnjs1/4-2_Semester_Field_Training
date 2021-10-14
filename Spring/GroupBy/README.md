# GROUP BY

- 일반적으로 특정 그룹(포지션별, 팀별)별 데이터를 필요로 할 경우 GROUP BY절을 그룹 함수와 함께 이용
- GROUP BY절 이용시, select에 지정한 컬럼은 group by 절에 모두 포함해야 한다.

```sql
select [distinct] 컬럼명 [as명]
from 테이블명
[where 조건식]
group by 컬럼이나 표현식;
```

## 그룹 함수

- 다중 행 함수의 일종으로 여러 행들의 그룹이 모여 단 하나의 결과를 돌려주는 함수
- where절에서는 group by를 위해 사용하는 그룹 함수를 사용할 수 없다. (Having 절 사용)

1. count(a) : a행의 갯수
2. max(a) : a행의 최대값
3. avg(a) : a행의 평균값

- ex. 선수들의 포지션별 평균키는 ?

```sql
SELECT POSITION, AVG(HEIGHT) FROM PLAYER GROUP BY POSITION;
```

## HAVING 절

```sql
 SELECT [DISTINCT] 컬럼명 [ALIAS명]

 FROM 테이블명

 [WHERE 조건식]

 GROUP BY 컬럼이나 표현식

 HAVING 그룹조건식;
```
- where절 : select ~ from 절에서 발췌된 데이터에 대한 제한 조건을 부여하여 필요한 데이터만을 조회할 때 사용하는 조건절
- having 절 : 그룹함수를 사용해 group by절을 사용할 때 그룹들에 대한 제한 조건이 필요하여 사용하는 그룹에 대한 조건절 

> select의 조건은 where절, group by절의 조건은 having 절

## HAVING 절 수행 순서

1. where : 발췌 대상 데이터 아닌 것은 제거
2. group by : 행들을 그룹화 하고,
3. group function : 그룹함수를 적용
4. having : 마지막으로 그룹 함수 값의 조건에 맞는 것만 발췌해서 출력

