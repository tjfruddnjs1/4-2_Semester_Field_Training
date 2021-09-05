# React(ITZ-WEB)

## 1주차 : 기존 동작하던 기능 개선 및 코드 리팩토링

> 진행했던 순서대로 맡은 부분에 대해 설명하겠습니다.

### 채팅방 관리 기능 개선> redux 관련 코드 리팩토링

- 이전까지는 아래의 사진과 같은 구조였습니다.
- <img src="https://user-images.githubusercontent.com/41010744/132116832-7491ef62-9a65-4444-9172-571f62d9fac0.png">
- 코드 리뷰를 통해 위의 구조를 redux 폴더는 실제 state 관리만을 위해 firebase 접근을 막는 구조로 수정하기로 하였습니다.
- 더 자세하게는 기존 api/index.jsx에서는 firebase ref, get 메서드만 이용하여 firebase에서 data를 가져오고 수정/삭제 역할을 redux폴더에서 진행하였는데 수정/삭제 역할도 api에서 불러올 수 있도록 수정하였습니다.
- 구조 수정후 api폴더내에 포괄적으로 기능을 수행하던 index.jsx를 chat.jsx , admin.jsx, user.jsx 와 같이 가독성있고 전체를 불러오는 것이 아닌 필요 부분만 가져올 수 있도록 수정하였습니다.

### 기능 오류 수정

1. 1:1 채팅 list/edit 화면에서 닉네임이 서로 다름

- <img src="https://user-images.githubusercontent.com/41010744/132120316-1d09579e-f6f9-4745-928c-bef788baf009.png">
- 기존 코드에서 index 처리를 잘못하여 채팅의 user를 보여주는 부분에서 닉네임과 userPhotoRef가 잘못 지정되어 있는 오류가 있었는데 해당 사항을 수정하였습니다.

2. 좌측 메뉴바 활성화/비활성화 오류

- <img src="https://user-images.githubusercontent.com/41010744/132120253-7de0e939-3a70-4988-815e-d41180d57a32.png">
- 위의 그림과 같은 메뉴바에서 현재 보여지는 페이지를 나타내는 active 역할이 잘못 지정되어 있었습니다. 템플릿 내의 메뉴바 active 사용 조건을 수정함으로써 해결하였습니다.

### 채팅방 편집 화면 개선

- <img src="https://user-images.githubusercontent.com/41010744/132120398-b860785f-7575-4f23-adf4-b19de8475755.png">
- 위의 사진 체크박스 항목들을 수정하도록 할당 받았고 간단한 수정이었기에 빠르게 수정하였습니다.
- <img src="https://user-images.githubusercontent.com/41010744/132122888-5a9c45ae-f6f4-4f8d-87d4-f2e6e2d769e0.png">
- 그리고 WAS에 빌드하는 과정에서 메시지의 opacity가 0으로 설정 되어 채팅 내용이 나오지 않는 문제가 있었는데 이는 아래의 링크를 통해 해결하였습니다.
- https://stackoverflow.com/questions/58853844/the-opacity-value-was-changed-to-1-after-building-the-reacjs-project

### 채팅방 관리 개선

- <img src="https://user-images.githubusercontent.com/41010744/132121565-85cd5825-5b4c-4dfb-8958-0907bb21f977.png">
- 위의 사진 체크 박스 항목들을 수정 받았고 간단한 수정이었기에 빠르게 수정하였습니다.
- 결과는 아래와 같습니다.

<table>
<tr>
<td><img src="https://user-images.githubusercontent.com/41010744/132121759-f58e8260-2b89-4aa8-96f3-d6fb43ab2732.png"></td>
<td><img src="https://user-images.githubusercontent.com/41010744/132122484-7d2b9c90-d9e8-4324-b775-3e0cb65d0e52.png"></td>
</tr>
</table>

### 아이템 삭제 출력 오류 수정

- 채팅 메시지를 2개 이상 삭제 시 1개만 삭제된 것처럼 출력되는 오류가 있었습니다.
- 실제로는 2개가 삭제되어 있어 새로고침 시 정상이었고, 이는 삭제 후 state를 갱신하는 함수를 takeLatest로 호출해성 발생하였고 takeEvery로 호출하는 것으로 변경하여 수정하였습니다.

> takeEvery VS takeLatest

- **takeEvery** : 액션이 dispatch될 때 마다 새로운 task를 실행
- 항상 fork하기 때문에 동시에 호출될 수 있고, 실행 순서가 보장되지 않는다.
- dispatch된 액션에 대한 처리가 동시에 발생하기 때문에 해당 task가 동시에 중복으로 발생해도 문제가 없는 경우 사용
- **takeLatest** : 액션이 dispatch 됐을 때 이전에 이미 실행 중인 task가 있다면 새로운 task를 실행
- 즉, 항상 최신 액션이 처리되는 것을 보장
- 이미 처리 중인 상황이라도 동일한 액션이 dispatch되면 취소되고 다시 호출
- 보통 GET 요청이면서 파라미터에 따라 결과가 달라지는 api라면 takeLatest를 사용해주면 적절

## 2주차 : 기능 개선 및 추가

### 지도 메뉴 개선

- <img src="https://user-images.githubusercontent.com/41010744/132127631-55d951dd-be7b-4305-b3aa-03c8c9db7804.png">
- 기존 react-naver-map으로 부터 Circle을 추가하였습니다.
- circle의 위치는 입력받은 radius로부터 중심 지점만 list로부터 불러와 해당 반경에 맞는 위치에 나타내었습니다.

> 구현 결과

- <img src="https://user-images.githubusercontent.com/41010744/132127674-16800ee9-1e24-4a61-a62f-f88972258baa.png">

### 지도 화면에서 우클릭하여 해당 좌표의 채팅방 생성

- <img src="https://user-images.githubusercontent.com/41010744/132127772-4a70baee-cc32-4691-bd52-cad383737391.png">
- react-contextmenu의 contextMenu, MenuItem, ContextMenuTrigger를 이용하여 우클릭시 해당 컴포넌트를 불러오고 클릭 이벤트를 추가하였습니다.
- 또한 react-router-dom의 Link 컴포넌트를 통해 위, 경도 정보를 state 형태로 채팅방 생성 페이지로 보내주어 생성하는 페이지에서 해당 위치 정보를 받아올 수 있게 되었습니다.

### 지도 마커 클릭 시 기능 개선

- <img src="https://user-images.githubusercontent.com/41010744/132128054-e15071f9-3d9a-4b5d-be69-8c3c1a4fdac7.png">
- 지도 마커 클릭 시 기존에는 해당 채팅 방의 채팅 내용을 보여주었는데 위의 그림 처럼 클릭 시 팝업 창을 통해 나타내고 보여주는 기능을 맡았습니다.
- 기존 javascript를 이용한 naver-map api는 위의 기능들을 제공했지만 react에 특성에 맞게 오픈소스 형식으로 사용하게 만든 react-naver-map에서는 해당 기능을 제공하지 않았습니다. 위의 Context Menu 역시 동일했습니다.
- 따라서 reactstrap 의 Popover, PopoverBody, PopoverHeader 컴포넌트를 사용하여 map위에 띄워주는 방법을 사용해야 했습니다.
- 기존 list로부터 불러온 index의 동일 여부를 체크해 팝업 클릭 시 해당 index라면 Popover 컴포넌트를 return하는 방식으로 구현했습니다.

> 구현 결과

- <img src="https://user-images.githubusercontent.com/41010744/132128215-df661178-aebf-4e22-8396-84707185620b.png">

### 통계 화면 추가

- <img src="https://user-images.githubusercontent.com/41010744/132128557-b43261ea-95fb-409b-9466-ebc8e5442b68.png">
- 해당 기능을 구현하는데 있어 react-apexcharts와 moment node module을 이용했습니다.
- 먼저 주간(요일별) 메시지 수, 월간 메시지 수, 연간 메시지 및 채팅방 유저 이용자 수, 채팅방 총 메시지 수를 띄워주는 기능 구현을 할당받았습니다.
- react-apexcharts는 x축과 y축에 해당하는 데이터를 배열 형태로 넣어주면 되었고 moment로부터 timestamp 형태로 저장되어 있는 시간 데이터를 요일 , 월, 연도를 계산하여 불러와 그 수를 chart에 띄워주도록 하였습니다.

> 구현 결과

- <img src="https://user-images.githubusercontent.com/41010744/132128721-473be9d6-a17f-4316-9f4b-335cfec139c1.png">
