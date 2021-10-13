# Join

## SQL JOINS

<img src="https://user-images.githubusercontent.com/41010744/132785014-a6b80dc5-188f-4510-b03f-f59e673d7c4b.png">

- Inner Join : Table A, B의 교집합을 조회
- Outer Join : Table A, B의 합집합을 조회

## 예시

<img src="https://user-images.githubusercontent.com/
41010744/132785227-13850d95-429f-4f5b-8241-279d265dd49e.png">

- table a : 사물의 이름 저장
- table b : 무게를 저장
- rel_table_a_b : 사물과 무게의 관게를 표현
- 위와 같이 테이블 3개가 존재한다고 할 때 , Left Join 결과

<img src="https://user-images.githubusercontent.com/41010744/132785822-8ec81d58-e167-4449-9a7a-ce19c05dfb4b.png">

- Left Join은 table_a에 있는 모든 행과 rel_table_a_b에 함께 있는 행을 모두 얻게 된다
- 따라서, table_a 값 중 relation table에 포함되지 않는 delta, east도 함께 조회

- 아래는 Inner Join 결과
  <img src="https://user-images.githubusercontent.com/41010744/132786290-78e42fe1-33fc-440a-b1f1-f8d46f97d9ad.png">
- table_a의 idx와 rel_table_a_b의 a_idx 값이 동일한 값들만 조회
- 따라서, 교집합에 해당하는 apple, bob, charlie만 가져온다

> 경우에 따라 다르겠지만, left join보다 inner join을 사용할 경우 추가적으로 join 또는 연산해야 하는 target data의 수가 줄기 때문에 left join보다 query 성능을 위해 inner join을 사용하는 것이 좋다.
