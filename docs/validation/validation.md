# 검증
 - 팁 : 타입 시스템을 이용하면 컴파일 타임에 에러를 발견할 수 있다. 
 - 검증과 관련하여 GraphQL에서 사용 가능한 케이스와 불가능한 케이스를 보자.


## 해당 페이지를 볼 때 알아두면 좋은 정보들

```typescript
interface Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
}

type Query {
  hero(episode: Episode): Character
  droid(id: ID!): Droid
}

type Human implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  starships: [Starship]
  totalCredits: Int
}

type Droid implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  primaryFunction: String
}
```



## Query 속 Fragment가 존재하는 경우.
#### 가능케이스
 ```javascript
{
  hero {
    ...NameAndAppearances
    friends {
      ...NameAndAppearances
      friends {
        ...NameAndAppearances
      }
    }
  }
}

fragment NameAndAppearances on Character {
  name
  appearsIn
}
```

#### 불가능 케이스 

```javascript
{
  hero {
    ...NameAndAppearancesAndFriends
  }
}

fragment NameAndAppearancesAndFriends on Character {
  name
  appearsIn
  friends {
    ...NameAndAppearancesAndFriends
  }
}
```
> Fragment는 자기자신을 참조할 수 없음.

## Query에 정의되지 않은 필드를 조회하려는 경우.
```javascript
{
  hero {
    favoriteSpaceship
  }
}
```
> hero는 Character를 반환하기 때문에 Character에 존재하는 필드를 쿼리해야함.

## 아무것도 조회하지 않을 때
```javascript
{
  hero
}
```
> 조회할 하나 이상의 필드를 명시 해야함.

##  스칼라 타입에 추가적인 타입을 요청하는 경우
```javascript
{
  hero {
    name {
      firstCharacterOfName
    }
  }
}
```
> GraphQL의 쿼리는 [항상 스칼라 타입으로 끝나야 하며](https://graphql-kr.github.io/learn/execution/#r101)
> 스칼라 하위 타입은 존재하지 않는다.
> [GraphQL의 Scalar type](https://graphql-kr.github.io/learn/schema/#r103)

## Query에 존재 할 수도, 안할수도 있는 타입을 쿼리 하려고 할 때
#### 불가능 케이스
```javascript
{
  hero {
    name
    primaryFunction
  }
}
```
> `type Droid implements Character`
> 위와 같이 Droid는같이 Character 타입이기 때문에 `primaryFunction` 필드를 갖지만
> `interface Character` 에는 `primaryFunction` 필드가 존재하지 않음.

#### 가능케이스
```javascript
// 가능 케이스 1
{
  hero {
    name
    ...DroidFields
  }
}

fragment DroidFields on Droid {
  primaryFunction
}

// 가능 케이스 2
{
  hero {
    name
    ... on Droid {
      primaryFunction
    }
  }
}
```
> Query한 결과가 Droid으로 표현 가능할 경우에만 노출 하도록 변경함.
> 케이스 1처럼 Fragment로 표현 가능하지만,
> 케이스 2처럼 인라인으로도 표현 가능함