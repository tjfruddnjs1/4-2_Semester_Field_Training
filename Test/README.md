# 13주차 부하 테스트 (시스템 성능 검증)

- 성능 검증 기준 제시

<img src="https://user-images.githubusercontent.com/41010744/143519896-96c6f276-08b2-402f-b36d-2a5a909e6b69.png">

- Number of Threads (사용자 수), Ramp-up period(응답 시간), Loop Count(반복 횟수) - 부하를 위한 loop 수 지정

<img src="https://user-images.githubusercontent.com/41010744/143530342-d89b4819-c509-4f00-a3c0-9fec4f3168f0.png">

- request 경로 > domain 및 테스트하고자 하는 페이지 경로 지정

<img src="https://user-images.githubusercontent.com/41010744/143531019-b5fb1fd3-8145-4546-906e-2933fa179db6.png">

### 테스트 결과 화면

- `1, 2번 사진` : result 결과 화면과 결과 Excel 파일 1번 사진의 Load Time이 Excel 파일의 `elapsed` 라는 이름으로 존재
- 검증 기준의 `4초 이내`에 통과하는 모습 (elapsed 135 -> 1.35초)

- `3번 사진` : Transcations per Second(초당 트랜잭션 처리 수) => 평균 `60~70` 사이 모습
- 검증 기준의 `최소 25`에 통과하는 모습

<table>
<tr>
<td><img src="https://user-images.githubusercontent.com/41010744/143531281-575931d2-4723-4a7f-abdb-f7d618d8e26b.png"></td>
<td><img src="https://user-images.githubusercontent.com/41010744/143531234-f432ac0f-f26f-4f94-b069-aacb3ca1082c.png"></td>
<td><img src="https://user-images.githubusercontent.com/41010744/143531323-e5454980-f60e-47b5-95ab-2547cf2faf1f.png"></td>
</tr>
</table>

#### Apache JMeter Downlad 사이트 : https://jmeter.apache.org/download_jmeter.cgi

#### JMeter 설치 및 사용 참고 사이트 : https://soccerda.tistory.com/190

#### Report 변환 참고 사이트 : https://digndig.kr/jmeter/2021/04/09/Jmeter_CreateHTMLReport.html