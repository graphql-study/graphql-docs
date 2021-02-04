## 스키마 확인

해당 페이지는 
[요 링크](http://graphql.github.io/swapi-graphql/?query=%7B%0A%20%20__type(name%3A%20%22Planet%22)%20%7B%0A%20%20%20%20name%0A%20%20%20%20kind%0A%20%20%7D%0A%7D)로 접속 하시어 테스트를 진행 하시면서 확인 하시면 
좀 더 쉽게 이해하실 수 있습니다.

> 타입 시스템을 사용하기 때문에 우리는 어떠한 타입들이 사용 가능한지 알고 있음.
> 하지만 종종 어떤 타입이 어떤 정보를 갖고 있는지? 
> 사용 가능한 타입인지 잘 모를때가 있음.
 > 이럴 때 `__schema` 필드를 사용하여 쿼리를 할 수 있음.
```javascript
{
  __schema {
    types {
      name
    }
  }
```
> 모든 스키마 확인 타입은 언더바 두 개(`__`)로 시작함 
위처럼 질의하면 우리가 추가적으로 사용 가능한 타입들이 내려옴. 
```javascript
{
  "data": {
    "__schema": {
      "types": [
        {
          "name": "Query"
        },
        {
          "name": "String"
        },
        {
          "name": "ID"
        },
        {
          "name": "Mutation"
        },
        {
          "name": "Episode"
        },
        {
          "name": "Character"
        },
        {
          "name": "Int"
        },
        {
          "name": "LengthUnit"
        },
        {
          "name": "Human"
        },
        {
          "name": "Float"
        },
        {
          "name": "Droid"
        },
        {
          "name": "FriendsConnection"
        },
        {
          "name": "FriendsEdge"
        },
        {
          "name": "PageInfo"
        },
        {
          "name": "Boolean"
        },
        {
          "name": "Review"
        },
        {
          "name": "ReviewInput"
        },
        {
          "name": "Starship"
        },
        {
          "name": "SearchResult"
        },
        {
          "name": "__Schema"
        },
        {
          "name": "__Type"
        },
        {
          "name": "__TypeKind"
        },
        {
          "name": "__Field"
        },
        {
          "name": "__InputValue"
        },
        {
          "name": "__EnumValue"
        },
        {
          "name": "__Directive"
        },
        {
          "name": "__DirectiveLocation"
        }
      ]
    }
  }
}
```
다음은 타입 시스템에서 정의한 것들임. 
 - Query
 - Character
 - Human
 - Episode
 - Droid

다음은 타입 시스템이 제공하는 내장 스칼라타입임.
 - String
 - Boolean

다음처럼 밑줄 두 개로 시작하는 타입은 `스키마 확인` 시스템의 일부임
 - `__Schema`
 - `__Type`
 - `__TypeKind`
 - `__Field`
 - `__InputValue`
 - `__EnumValue`
 - `__Directive`

#### 쿼리가 시작되는 타입
```javascript
// Query
{
  __schema {
    queryType {
      name
    }
  }
}

// Result
{
  "data": {
    "__schema": {
      "queryType": {
        "name": "Query"
      }
    }
  }
}
```

###  타입 검사
위에서 [테스트 해볼 수 있었던 사이트](http://graphql.github.io/swapi-graphql/?query=%7B%0A%20%20__type(name%3A%20%22Person%22)%20%7B%0A%20%20%20%20name%0A%20%20%20%20fields%20%7B%0A%20%20%20%20%20%20name%0A%20%20%20%20%20%20type%20%7B%0A%20%20%20%20%20%20%20%20name%0A%20%20%20%20%20%20%20%20kind%0A%20%20%20%20%20%20%7D%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D)에 접속하여 아래와 같이,
`특정필드(Person과 같은..)` 타입에 대한 정보를 조회 해볼 수 있음.
```javascript
{
  __type(name: "Person") {
    name
    description
    fields {
      name
      type {
        name
        kind // __TypeKind의 열거형을 반환함.
        ofType { // List와 같이 wrapper 타입인 경우에 정확한 타입을 알고 싶을 때 사용
          name
          kind
        }
      }
    }
  }
}
```

위처럼 쿼리할 경우 `Person`에서 정의한 필드 목록을 모두 볼 수 있음. [`(쿼리결과)`](http://graphql.github.io/swapi-graphql/?query=%7B%0A%20%20__type(name%3A%20%22Person%22)%20%7B%0A%20%20%20%20name%0A%20%20%20%20fields%20%7B%0A%20%20%20%20%20%20name%0A%20%20%20%20%20%20type%20%7B%0A%20%20%20%20%20%20%20%20name%0A%20%20%20%20%20%20%20%20kind%0A%20%20%20%20%20%20%7D%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D)