# Order by

```sql
 SELECT [DISTINCT] 컬럼명 [ALIAS명]

 FROM 테이블명

 [WHERE 조건식]

 [GROUP BY 컬럼이나 표현식

 HAVING 그룹조건식]

 ORDER BY 칼럼이나 표현식[ASC 또는 DESC];
```

- 데이터를 보여줄 땐 정렬을 해야 보는 사람이 편하게 볼 수 있으며, 결과에 대한 분석을 빠르게 할 수 있다.
- 이를 가능케 하는 것이 order by 절로, 조회된 결과의 데이터를 정렬하여 보기 좋게 만들어 준다.

> 기본적으로 order by는 오름차순 정렬이 되어 asc 생략이 가능

- 또한 모든 절을 다 이용한다면, 작성 순서는 where 절 > group by 절 > order by 절 순서
- order by절에서는 select 문에서 조회한다고 지정한 모든 항목을 입력 가능 (avg, count 도 가능)
