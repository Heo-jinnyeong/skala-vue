## 과제 1: Weather Mockup

Vue의 배열 렌더링, 조건부 렌더링, 양방향 바인딩과 이벤트 처리를 활용해 지역별 날씨 화면을 구현했습니다.

### 추가 데이터

기본 데이터인 서울, 수원, 부산 외에 광주와 대구의 날씨 데이터를 추가했습니다. 광주는 23℃의 바람 상태, 대구는 31℃의 폭염 상태로 설정하여 온도에 따라 서로 다른 날씨 라벨이 표시되도록 구성했습니다.

## 과제 2: Weather Composition

Vue Composition API의 `ref`, `computed`, `watch`, `watchEffect`를 활용해 도시 검색 결과를 실시간으로 필터링하고, 선택된 도시와 검색어의 변화를 감시하는 지역별 날씨 화면을 구현했습니다.

### 추가 기능

기본 도시 검색 기능 외에 25℃ 이상인 도시만 확인할 수 있는 필터 기능을 추가했습니다. `showHotCitiesOnly`를 반응형 상태로 관리하고, `displayedWeatherList`에서 검색 결과와 온도 조건을 함께 계산하도록 구성했습니다. 체크박스로 필터를 켜거나 끌 수 있으며, 설정이 변경될 때마다 `watch`를 통해 현재 활성화 상태가 콘솔에 출력됩니다.

## 과제 3: Weather Component

추가 Task TODO

## 과제 4: Weather Router

추가 Task TODO

## 과제 5: Weather Store

추가 Task TODO
