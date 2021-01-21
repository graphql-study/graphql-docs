# Big Picture(Architecture)

> https://www.howtographql.com/basics/3-big-picture/

다음과 같은 Use Case가 있습니다 (3)

1. 데이터베이스가 있는 GraphQL 서버
2. 기존 레거시 시스템을 통합(추상화) 시키는 GraphQL API layer
3. 하이브리드 접근 (1번과 2번 통합)

## GraphQL server with a connected database

![](https://imgur.com/cRE6oeb.png)

- GraphQL을 활용해 (웹)서버를 만드는 가장 일반적이 경우
- 클라이언트로부터 쿼리(payload, =request)가 오면 graphQL서버는 DB로 부터 값을 받아 클라이언트에게 [map형식](https://spec.graphql.org/June2018/#sec-Response)으로 전달 (This is Called `resolving the query`)

- About Network: Graphql is `transport-layer agnostic`
> 일반적으로 gql의 인터페이스간 송수신은 네트워크 레이어 L7의 HTTP 와 웹소켓 프로토콜을 활용합니다. 필요에 따라서는 얼마든지 L4의 TCP/UDP를 활용하거나 심지어 L2 형식의 이더넷 프레임을 활용 할 수도 있습니다. [kakao tech](https://tech.kakao.com/2019/08/01/graphql-basic/)

- About DB: Graphql doesn’t care about the database or the format that is used to store the data
  - SQL, NoSQL, Cloud Storage

## GraphQL layer that integrates existing systems

- 기존 레거시 시스템의 복잡성을 추상화 시킬 수 있음

![](https://imgur.com/zQggcSX.png)

## Hybrid approach with connected database and integration of existing system

- DB + Legacy system

![](https://imgur.com/73dByTz.png)


## Resolver
> GraphQL이 이렇게 유연한 이유

![](https://imgur.com/e1gBEP5.png)

- GraphQL Query (또는 Mutation)의 페이로드는 필드들의 집합으로 구성 됩니다.
- 페이로드의 각 필드들은 각각 resolver 함수에 할당 됩니다. 
- 즉 **리졸버는 전달 받은 페이로드에서 각 필드들의 데이터를 (원하는 타입에 맞게)뽑아내는 함수**
  - 실제 사용해봤을때는, 필드단위가 아닌 스키마(DTO) 단위로 resolver를 생성해줬는데, 문구 확인이 필요
- 이후 GraphQL 서버는 모든 리솔버들의 반환값을 패키징(원하는 형식)하여, 클라이언트로 다시 보냅니다.

## Graphql Client Libraries


- GraphQL의 Client에서 Best Practice
  - 이상적인 (GraphQL)클라이언트는 다음 2가지 기능만 해야함.

1. 필요 데이터 요구 사항 작성(서버랑 주고 받을 스키마 명세 작성)
2. UI에 데이터 표시 (response 받은 데이터 display)

- 모든 하위 수준 네트워킹 작업과 데이터 저장(stores data local memory or persistent)은 추상화되어야함. (Apollo, Relay)
- **the declaration of data dependencies should be the dominant part** ---> Q.이게 주력이 되어야 한다는데 무슨뜻?