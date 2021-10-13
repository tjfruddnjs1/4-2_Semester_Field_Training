# ThymeLeaf

> 템플릿 엔진이란 ?

- 동적 컨텐츠를 생성하는 방법
- 템플릿 양식과 특정 데이터 모델에 따른 입력 자료를 결합하여 결과 문서를 출력하는 소프트웨어
- view와 data logic code(db connection)을 분리해주는 기능
- 스프링 MVC에서는 주로 동적인 View를 만드는데 사용

> VS JSP

- HTML 태그에 <% %> 스크립트릿 사용
- JSP 사용으로 인해 Web Application 배포 시 WAR 파일로 만들어진다
- WAR는 웹 애플리케이션 압축 타입으로 Servlet 해석과 관련된 모든 패키지들을 포함시키면서 복잡하고 무거운 구조

## Thymeleaf 사용

> 네임스페이스 추가

- `<html lang="en" xmlns:th="http://www.thymeleaf.org">`

> 기본 사용법

1. 변수 : ${}
2. 객체 변수값 : \*{}

ex.

<img src="https://user-images.githubusercontent.com/41010744/136871353-6b58df89-612a-4e29-b862-c54ce01416f1.png">

<img src="https://user-images.githubusercontent.com/41010744/136871398-1a1d0987-4d6e-428c-8737-0a7488f0afdd.png">

3. 메시지 : #{}
4. 링크 : @{}

```html
<!-- Will produce 'http://localhost:8080/gtvg/order/details?orderId=3' (plus rewriting) -->
<a
  href="details.html"
  th:href="@{http://localhost:8080/gtvg/order/details(orderId=${o.id})}"
  >view</a
>
<!-- Will produce '/gtvg/order/details?orderId=3' (plus rewriting) -->
<a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>
<!-- Will produce '/gtvg/order/3/details' (plus rewriting) -->
<a href="details.html" th:href="@{/order/{orderId}/details(orderId=${o.id})}"
  >view</a
>
```

ex.

> application.properties

```java
api:
    url: https://www.test.co.kr
```

> messages.properties

```java
main.title=Hello World
```

> Model

```java
@Data
public class UserModel {
    private int seq; // 회원고유번호
    private String id; // 회원아이디
    private String userName; // 회원명
    private String email; // 이메일
    private String phone; // 핸드폰번호
    private String sex; // 성별
    private Date createDt; // 등록일자
    }
```

> Controller

```java
@Controller public class userController {
    @Autowired UserService userService;
    @GetMapping("/userList")
    public String getUserList(Model model, userModel user) {
        // 회원리스트 가져오기
        List<userModel> userList = UserService.getUserList(model);
        model.addAttribute("userList", userList);
        model.addAttribute("title", 'Thymeleaf 소개');
        return "userList";
    }
}
```

> HTML

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <title th:text="${title}">Title</title>
  <h1 th:text="#{main.title}"></h1>
  <h1 th:text="${@environment.getProperty('api.url')}"></h1>
  <body>
    <table>
      <tr>
        <th>이름</th>
        <th>아이디/이메일</th>
        <th>핸드폰번호</th>
        <th>성별</th>
        <th>등록일자</th>
      </tr>
      <tr th:each="user : ${userList}">
        <td th:text="${user.userName}"></td>
        <td
          th:text="|${user.id + ' / ' + user.email}|"
          th:href="@{'user/'+user.id}"
        ></td>
        <td th:text="${user.phone}"></td>
        <td th:text="${user.sex == 'M' ? '남자' : '여자' }"></td>
        <td th:text="${#dates.format(user.createDt, 'yyyy-MM-dd HH:mm')}"></td>
      </tr>
    </table>
  </body>
</html>
```

> 문자 더하기

1. '|' 와 '|' 연산자 사이에 문자와 변수 표현식을 입력

- static 문자와 변수 문자가 이어져서 출력

```html
<span th:text="|Welcome to our application, ${user.name}!|"></span>
```

2. '+' 연산자를 이용해 문자를 더하기

```html
<span th:text="'Welcome to our application, ' + ${user.name} + '!'"></span>
```

3. 혼합형

```html
<span th:text="${onevar} + ' ' + |${twovar}, ${threevar}|"></span>
```

> Defalut 연산자 (Elivs operator)

- 엘비스 연산자는 3항 연산자 문법의 단축형

```js
var x = f() ? f() : g();
```

- 위와 같은 3항 연산자를 다음과 같이 단축해서 사용하는 방법

```js
var x = f() ?: g()
```

- 기본값으로 g()을 가지겠다는 의미

- thymeleaf에서는 다음과 같이 사용

```html
<div th:object="${session.user}">
  ...
  <p>Age: <span th:text="*{age}?: '(no age specified)'">27</span>.</p>
</div>
```

> 속성값 설정

1. th:attr을 통해 원하는 속성 여러개를 동시에 설정

```html
<img
  src="../../images/gtvglogo.png"
  th:attr="src=@{/images/gtvglogo.png},title=#{logo},alt=#{logo}"
/>
```

2. th:value, th:action, th:href 등 속성을 지정해 별도로 설정

```html
<img
  src="/gtgv/images/gtvglogo.png"
  title="Logo de Good Thymes"
  alt="Logo de Good Thymes"
/>
```

> 속성값 append(뒤에 붙이다) OR prepend(앞에 붙이다)

ex. 변수 cssStyle 값이 'warning`인 상태에서 다음과 같이 표현하면,

```html
<input
  type="button"
  value="Do it!"
  class="btn"
  th:attrappend="class=${' ' + cssStyle}"
/>

<input type="button" value="Do it!" class="btn warning" />
```

처럼 class 속성 뒤에 warning이 붙어 설정하게 된다.

- class뿐만 아니라 모든 속성을 이와 같은 방법으로 사용할 수 있으며, class는 th:classappend를 사용하여 속성명을 생략하고 사용 가능

> 반복문

- 반복 기능은 th:each 또는 data-th-each 속성으로 사용이 가능하며 반복에 사용가능한 변수 타입은 아래와 같습니다

1. java.util.ArrayList 나 java.util.HashSet 처럼 java.util.iterable 인터페이스를 구현한 객체
2. java.util.Map 인터페이스를 구현한 객체 (반복되는 객체는 java.util.Map.Entry)
3. 모든 배열

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
  </tr>
  <tr th:each="prod,iterStat : ${prods}" th:class="${iterStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
  </tr>
</table>
```

- 반복을 돌며 각 index 마다 상태값을 가져올수도 있는데 iter 변수 옆에 ','로 추가하여 status 변수를 정의하고 사용 가능
- 위의 예제의 iterStat라는 변수를 통해 odd(홀수)이면 'odd' 클래스를 적용
- odd외의 다양한 상태값을 가지는데 상태값으로는 아래와 같습니다.

1. index - 0부터 시작하는 index

2. count- 1부터 시작하는 index

3. size - 리스트의 size

4. current - 현재 index의 변수

5. event/odd - 짝수/홀수 여부

6. first/last- 처음/마지막 여부

> 조건문

- 특정 조건일때만 보여지는 영역이 필요할 때 조건 속성을 통해 해당 영역을 rendering을 안하게 할 수 있다
- th:if 속성을 통해 사용이 가능하며 th:if와 반대인 th:unless 도 사용 가능

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
  </tr>
  <tr th:if="${#lists.size(prods)} > 0" th:each="prod,iterStat : ${prods}">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}">yes</td>
  </tr>
  <tr th:unless="${#lists.size(prods)} > 0">
    <td colspan="3">No Data.</td>
  </tr>
</table>
```

- 위와 같이 prods의 size가 0인 경우 No Data, 0보다 큰 경우 prods 값들 출력
- th:if, th:unless 외에 th:switch/th:case 속성도 다음과 같이 사용 가능

```html
<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="#{roles.manager}">User is a manager</p>
  <p th:case="*">User is some other thing</p>
</div>
```

> fragment

- JSP의 include 처럼 다른 template의 특정 영역을 가져와 나타낼 수 있다
- 현재 template의 특정 영역도 사용이 가능

ex. 홈페이지 하단의 footer 영역 공통 지정 (th:fragment 이용)

- WEB-INF/templates/footer.html 이름으로 아래 파일 지정

```html
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
  <body>
    <div th:fragment="copy">&copy; 2011 The Good Thymes Virtual Grocery</div>
  </body>
</html>
```

- `th:fragment="copy"` 속성을 다른 페이지에서 사용하기 위한 아래 코드

```html
<body>
  ...

  <div th:include="footer :: copy"></div>
</body>
```

- (템플릿명) :: (fragment 명) 형식으로 가져올 수 있다
- 이 때, footer가 템플릿 명인데 thymeleaf 템플릿 엔진을 초기화 할때 템플릿 prefix를 `/WEB-INF/templates/` 로 설정했고 suffix를 `.html`로 설정했기 때문에 footer라고 사용 가능
- 만약 별도로 setPrefix, setSuffix를 설정하지 않았으면 `/WEB-INF/templates/footer.html :: copy` 라고 정의해야 한다.

ex. 홈페이지 하단의 footer 영역 공통 지정 (id 이용)

```html
...
<div id="copy-section">&copy; 2011 The Good Thymes Virtual Grocery</div>
...

<body>
  ...

  <div th:include="footer :: #copy-section"></div>
</body>
```

> assert

- th:assert 속성을 사용하며 조건을 ',' 연결하여 여러 조건을 사용 가능
- 모든 조건이 true 가 아니라면 exception 발생

```html
<div th:fragment="frag">
  <p
    th:assert="${onevar}=='a',${twovar}=='b'"
    th:text="${onevar} + ' - ' + ${twovar}"
  >
    ...
  </p>
</div>
```

> 지역 변수 (Local Variables)

- Thymeleaf에서 지역변수의 범위는 변수가 정의된 DOM을 포함한 하위의 DOM까지 포함

```html
<tr th:each="prod : ${prods}">
  <td th:text="${prod.name}">Onions</td>
  <td th:text="${prod.price}">2.41</td>
</tr>
```

- 위 fragment는 each 속성을 통한 반복구문인데 여기에 사용된 `prod`는 `<tr>`을 포함하여 그 하위의 태그에서만 사용 가능
- 또한, th:with 속성을 이용하여 지역변수를 사용할 수 있다

```html
<div th:with="firstPer=${persons[0]},secondPer=${{persons[1]}">
  <p>
    The name of the first person is <span th:text="${firstPer.name}">Tom</span>.
    The name of the second person is
    <span th:text="${secondPer.name}">Jeny</span>.
  </p>
</div>

<div th:with="company=${user.company + ' Co.'},account=${accounts[company]}">
  ...
</div>
```

- ,를 구분자로 여러개의 지역변수를 선언할 수 있고 위와 같이 동일한 속성에 정의된 변수의 재사용이 가능
- 또한 아래와 같이 th:with는 우선순위가 높기 때문에 하나의 태그의 다양한 속성 내에서 사용이 가능

```html
<p>
  Today is:
  <span th:with="df=#{date.format}" th:text="${#calendars.format(today,df)}"
    >13 February 2011</span
  >
</p>
```

- df라는 변수를 정의하고 th:text에서 df 변수의 사용이 가능
- 여기에서 날짜데이터를 바인드 시키기위해 span 태그가 사용이 되었는데 span 태그가 불필요 하다면 span 대신 th:block을 사용

| 우선순위 | 속성                                    | 설명                            |
| -------- | --------------------------------------- | ------------------------------- |
| 1        | th:include, th:replace                  | Fragment inlusion               |
| 2        | th:each                                 | Fragment iteration              |
| 3        | th:if, th:unless , th:switch , th:case  | Conditional Evaluation          |
| 4        | th:object, th:with                      | local variable definition       |
| 5        | th:attr ,th:attrprepend , th:attrappend | general attribute modification  |
| 6        | th:value th:href th:src                 | Specific attribute modification |
| 7        | th:text , th:utext                      | Text(tag body modification)     |
| 8        | th:fragment                             | Fragment specification          |
| 9        | th:remove                               | Fragment removal                |

> 주석 & 블록

- HTML과 XML에서 사용되는 표준 주석 구문인 <!-- ... -->는 thymeleaf에서 별도로 처리하지 않고 그대로 표현
- 하지만 thymeleaf에서만 처리되는 특수한 주석 2가지 종류가 존재

1. `Parser-level` 주석

- 정적인 페이지에서 주석으로 남겨져있다가 thymeleaf 처리가 될 때 제거되는 주석
- `<!--/* */-->` 사이의 내용은 thymeleaf 처리후 제거가 되므로 불필요한 주석을 클라이언트에 노출시키지 않는다.
- 이 뿐만 아니라 다음과 같이 정적인 페이지에서 DOM을 표현했다가 thymeleaf 처리후 제거가 되도록 표현이 가능 > `th:remove 속성`과 유사

```html
<!--/*-->
<div>you can see me only before thymeleaf processes me!</div>
<!--*/-->

<table>
  <tr th:each="x : ${xs}">
    ...
  </tr>
  <!--/*-->
  <tr>
    ...
  </tr>
  <tr>
    ...
  </tr>
  <!--*/-->
</table>
```

2. `Prototype-Only` 주석

- Parser-level 주석과 반대되는 개념의 주석
- 정적페이지에서 주석으로 처리가 되고 thymeleaf 처리후 나타나게 되는 주석

```html
<span>hello!</span>
<!--/*/
  <div th:text="${...}">
    ...
  </div>
/*/-->
<span>goodbye!</span>
```

- 정적 페이지에선 Hello! goodbye! 만 보여지게 되지만 thymeleaf 처리가 되면

```html
<span>hello!</span>

<div th:text="${...}">...</div>

<span>goodbye!</span>
```

- th:block은 prototype-only 주석과 함께 사용될 때 특히 유용한데, 아래의 예시처럼 prototype-only 주석과 함께 사용하게 되면 정적인 페이지에선 2개의 `tr`만 보였다가 thymeleaf 처리가 되면 1번째 예시처럼 반복 처리

```html
<table>
  <!--/*/ <th:block th:each="user : ${users}"> /*/-->
  <tr>
    <td th:text="${user.login}">...</td>
    <td th:text="${user.name}">...</td>
  </tr>
  <tr>
    <td colspan="2" th:text="${user.address}">...</td>
  </tr>
  <!--/*/ </th:block> /*/-->
</table>
```

> Inlining

1. Text Inlining

- th:text 속성을 사용하여 대부분의 문자 처리가 가능하지만 HTML에 직접 표현 가능
- 아래 두개는 같은 표현

```html
<p>Hello, <span th:text="${session.user.name}">Sebastian</span>!</p>

<body th:inline="text">
  ...

  <p>Hello, [[${session.user.name}]]!</p>

  ...
</body>
```

- 하지만 이렇게 사용하게 되면 정적 페이지에서는 inline 표현식이 그대로 나타나게 된다.

2. Script Inlining(Javascript and Dart)

- 스크립트 inline 기능은 javascript와 Dart를 지원

```js
<script th:inline="javascript">
/*<![CDATA[*/
    ...

    var username = [[${session.user.name}]];

    ...
/*]]>*/
</script>
```

- 하지만 정적인 페이지로 열었을 때 스크립트 영역의 `[[..]]` 표현식은 문법 오류로 인식
- 따라서 아래와 같이 `/* [[]] */` 표현식을 사용하여 해결

```js
<script th:inline="javascript">
/*<![CDATA[*/
    ...

    var username = /*[[${session.user.name}]]*/ 'Sebastian';

    ...
/*]]>*/
</script>

```

- 정적 페이지에서는 주석처리되어 username 변수 값이 'Sebastian` 값으로 정의
- 하지만 thymeleaf 처리가 되면 `/*[[..]]*/` 구문에 포함된 표현식을 실행하게 되고 그 뒤에 있는 모든 코드는 제거되어 실제 사용자의 이름을 가져올 수 있다
- 아래와 같은 다양한 형식의 값을 위와 같이 표현 가능

```
Strings
Numbers
Booleans
Arrays
Collections
Maps
Beans (objects with getter and setter methods)
```

- thymeleaf 처리가 되었을때만 특정 스크립트를 추가하고 싶을때 사용할수 있는 기능으로 표현식은 다음과 같습니다

```js
var x = 23;

/*[+

var msg  = 'This is a working application';

+]*/

var f = function() {
    ...
```

- 위와 같이 /_[+ ... +]_/ 표현식으로 작성하게 되면 정적 페이지에서는 주석 처리되고 thymeleaf 처리가 되면 표현식 안의 스크립트 내용이 작성

#### 출처 : https://cyberx.tistory.com
