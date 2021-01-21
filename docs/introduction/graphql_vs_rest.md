# GraphQL is the better REST

> https://www.howtographql.com/basics/1-graphql-is-the-better-rest/

- REST API = Inflexible (클라이언트의 요구에 따라 빠르게 바꾸는 게 어렵다)
- GraphQL = Flexibility + Efficiency

## 1. Data Fetching with REST vs GraphQL

_예시: 블로그 (유저 데이터, 유저 포스트 제목, 최근 팔로워 세 명)_

- REST: 여러 엔드포인트로 데이터를 가져온다 (`/users/<id>`, `/users/<id>/posts`, `/users/<id>/followers`)
- GraphQL: Query를 하나 보내면 서버가 같은 구조를 가진 JSON으로 응답한다.
  ![](https://i.imgur.com/GgR9ZEM.png)

## 2. No more Over- and Underfetching

- REST에서는 정해진 데이터를 받는다
- Overfetching: 클라이언트가 필요 이상으로 많은 정보를 받는다
  - ex) 이름만 필요할 수 있지만, `/users` 엔드포인트에서는 더 많은 정보를 넘겨주는 경우
- Underfetching: 특정 엔드포인트가 필요한 정보를 전부 주지 않는다

## 3. Rapid Product Iterations on the Frontend

- REST: 생산성 저하 & 유저 피드백을 반영하는 게 느려진다
- GraphQL: 서버에서 별도의 작업 없이 클라이언트 측에서 변경

## 5. Benefits of a Schema & Type System

- GraphQL: Schema Definition Language (SDL)을 통해 타입 시스템을 사용
- 확실한 데이터 구조가 있기 때문에 프론트엔드와 백엔드가 소통 없이 손쉽게 각자 개발 가능

## 6. RPC vs. REST vs. GraphQL

![](https://i.imgur.com/8yuGYFL.png)

### 7. GraphQL의 단점

- 캐싱 관련 커스텀 로직을 추가해야 한다.
- 클라이언트 복잡도가 높아질 수 있다.
- 재귀적으로 데이터를 받지 못한다. (이 말이 결국 클라이언트 로직이 복잡해진다는 뜻)
- 적은 데이터를 받을 때는 GraphQL 프레임워크 관련 데이터들 때문에 오히려 비효율이 발생할 수 있다.
- HTTP Method (GET, POST 등)를 효율적으로 사용하지 못한다.

### Final Thoughts

- 보통 비교할 때 RESTful API를 굉장히 잘 나누어져 있는 상태로 생각하고 비교하는데, 현실은 아름답지 않기 때문에 한 화면에서 하나의 API를 받는 경우가 많고 (ex. 보여주기만 할 때) 이럴 때는 어떻게 비교 할 것인지 생각해보자.
- 모든 api를 gql로만 만든다는 게 과연 이점이 있을지도 생각해보자.
  - REST Api랑 같이 사용하는 경우도 있다.

### 기타 참고 자료

[https://medium.com/@FourwingsY/graphql을-오해하다-3216f404134](https://medium.com/@FourwingsY/graphql%EC%9D%84-%EC%98%A4%ED%95%B4%ED%95%98%EB%8B%A4-3216f404134)
