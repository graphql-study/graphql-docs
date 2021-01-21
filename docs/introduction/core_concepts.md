# 3. Core Concepts

```
- SQL
  - 데이터베이스 한정으로 쓰임
  - 데이터 테이블 안에 저장되어 있고, 조작하기 위해서 SELECT, INSERT, UPDATE, DELETE가 대표적

- GraphQL
  - API에서 쓰임
  - DB에도 저장되어있을 수 있고, 다른 파일 시스템, REST API로부터 데이터 fetch 가능
```

## The Schema Definition Language (SDL)

- GraphQL은 API의 스키마를 정의할 때 사용하는 타입시스템을 가지고 있다.
- 스키마를 작성하는 문법을 `SDL`이라고 한다.
- `!` 은 필수 값을 나타내는 기호로 사용된다.

### - 상위 객체 표현

```graphql
type Person {
	name: String!
	age: Int!
}

type Post {
	title: String!
}
```

### - 1:N 표현

```graphql
type Person {
	name: String!
	age: Int!
	posts: [Post!]!
}

type Post {
	title: String!
	author: Person!
}
```

- `posts` 필드값은 `one-to-many` 관계를 나타낸다.

### Data Fetcing, Query

- REST API에서는 한 endpoint가 반환해주는 데이터의 형식이 명확하게 정해져 있다. 특정 데이터를 요청할 때는 해당하는 URL로 요청을 보낸다.
- 그러나 GraphQL에서는 하나의 endpoint만을 가진다. 데이터의 형식이 정해져 있지 않고 클라이언트에서 필요한 데이터만을 flexible하게 가져올 수 있다.

```graphql
# Query
{
	allPersons {
		name
	}
}

# Return
{
  "allPersons": [
    { "name": "Johnny" },
    { "name": "Sarah" },
    { "name": "Alice" }
  ]
}
```

- Person이라는 데이터의 name 외의 age라는 프로퍼티가 있다하더라도 클라이언트는 **name만을 요청했으므로** 리턴해주는 데이터에서도 name만을 담아서 보내준다.
- GraphQL은 중첩되어 쿼리를 작성하는 것도 가능하다.

```graphql
{
  allPersons {
    name
    age
    posts {
      title
    }
  }
}
```

### Argument로 쿼리 날리기

- 각 필드는 0개 이상의 argument를 포함하여 더 구체적인 데이터를 요청할 수 있다.

```graphql
{
  allPersons(last: 2) {
    name
  }
}

# last 파라미터를 사용하여 가장 최근 2개의 데이터를 가져올 수 있다.
```

## Writing Data with Mutations

- 데이터를 요청하는 것 외에 백엔드에 저장된 데이터에 변경사항을 추가하는 경우가 있다.
- 데이터 생성, 데이터 갱신, 데이터 삭제 → 이 모든 것들을 `mutation` 이라고 부른다.
- mutations 쿼리를 보내려면 반드시 `mutation` 키워드로 시작해야 한다.

```graphql
mutation {
	createPerson(name: "Bob", age: 36, address: "seoul") {
		name
		age
	}
}
# mutation root 안에는 내가 다시 리턴받고 싶은 필드를 넣어둔다.

# server response
"createPerson": {
	"name": "Bob",
	"age": 36,
}
```

- `ID` 타입은 자동적으로 생성되는 유니크한 아이디를 의미한다.

## Realtime Updates with Subscriptions

- 발생하는 이벤트에 대해 즉각적으로 서버에서 정보를 받아야 하는 경우들이 증가함
이런 상황에서 사용할 수 있는 것이 GraphQL의 `subscriptions` 기능이다.
- 클라이언트가 특정 이벤트를 `subscribe` 하게 되면 서버와 steady한 연결이 맺어진다.
**구독하고 있는 mutation이 발생하면** 서버는 해당 데이터를 클라이언트에게 보내준다.
query나 mutation과 다르게 `stream of data` 를 나타내게 된다.
- 문법은 아래와 같다.

```graphql
subscription {
	newPerson {
		name
		age
	}
}

# connection 맺어짐
# 이벤트 발생!

{
	"newPerson": {
		"name": "Jane",
		"age": 23
	}
}
```

## 스키마 정의하기

- 스키마는 GraphQL API로 작업할 때 가장 중요한 개념 중 하나이다.
- 어떻게 클라이언트가 데이터를 요청할 것인지를 정의하고 API의 사용방법을 구체화한다.
- 서버와 클라이언트 간의 `contract`

```graphql
type Query {
  allPersons(last: Int): [Person!]!
}

type Mutation {
  createPerson(name: String!, age: Int!): Person!
}

type Subscription {
  newPerson: Person!
}

type Person {
  name: String!
  age: Int!
  posts: [Post!]!
}

type Post {
  title: String!
  author: Person!
}
```

## 추가 자료

- [GraphQL Server Basics (Part I): GraphQL Schemas, TypeDefs & Resolvers Explained](https://www.prisma.io/blog/graphql-server-basics-the-schema-ac5e2950214e)
- [GraphQL Server Basics (Part II): The Network Layer](https://www.prisma.io/blog/graphql-server-basics-the-network-layer-51d97d21861)
- [GraphQL Server Basics (Part III): Demystifying the `info` argument in GraphQL resolvers](https://www.prisma.io/blog/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a)