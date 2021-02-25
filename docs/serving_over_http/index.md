# HTTP를 통한 서빙

 - 서버 - 클라이언트가 데이터를 주고 받을 때 보통 HTTP를 사용하기 때문에 GraphQL 역시 HTTP를 이용하여 데이터를 주고 받도록 구현되어 있는 케이스가 대부분임.

 - 이번 챕터는 HTTP 프로토콜을 이용하여 GraphQL 서버를 세팅하기 위한 가이드를 제공함.



## Web Request Pipeline
 - 많은 프레임워크들이 다음 기능을 제공하고 있음.
   - `Middleware`
   - `Filter`
   - `Plugin`
> 이외에도 Interceptor 등 기능을 제공하고 있음.
> GraphQL은 인증 미들웨어 뒤쪽에 존재 해야하기 때문에, 기존에 서버가 접근하던 인증 정보에 접근 가능함.


## URIs, Routes

 - REST는 일반적으로 `리소스`를 사용하는 개념임.
 - GraphQL은 `엔티티 그래프`를 사용하는 개념임.

따라서 다음과 같은 차이점을 갖고 있음. 
 - GraphQL은 REST와 달리 URL을 이용하여 데이터를 식별하지 않음.
 - 단일 URL(`일반적으로 /graphql`)에서 실행됌.

## HTTP Methods, Headers, Body

다음 메서드를 사용 가능함. 
 - GET
 - POST

### GET
 GET 요청을 이용하려면, `query` 쿼리스트링을 사용해야함.
 다음과 같은 데이터가 있다고 가정하면
```javascript
{
  me {
    name
  }
}
 ```
다음과 같이 요청 해야함. 
```javascript
http://myapi/graphql?query={me{name}}
```

쿼리 변수가 필요할 경우 다음 변수를 이용하여, JSON 인코딩 된 문자열로 보낼 수 있음.
 - variables ([참고링크](https://graphql.org/graphql-js/passing-arguments/))

다음과 같이 보낼 수 있는 것 같음. 
```javascript
"variables": { "arg1": "me" }
```

 쿼리 변수에 개별적인 변수명을 사용하고 싶을경우 다음 이름을 사용할 수 있음.
 - operationName (`기존에 우리가 사용하던 쿼리스타일`)

## POST
GraphQL POST 요청은 다음 헤더를 포함해야함.
 - `Content-Type : application/json`

쿼리 전송 시 다음 필드들은 옵셔널 필드임.
 - operationName
    - query에 여러 작업이 필요한 경우에만 사용됌.
 - variables

다음 헤더가 있을경우, GraphQL Server는 HTTP POST body 내용을 GraphQL 쿼리 스트링으로 인식함
 - `Content-Type : application/graphql`

## Response
어떤 메서드(GET, POST)를 이용하여 데이터를 요청 했는지와는 관계 없이, 응답은 바디에 `JSON`으로 반환되어야 함.

에러가 발생했을 시 다음과 같이 `errors`필드가 반드시 포함되어야 한다.
```javascript
{
  "data": { ... },
  "errors": [ ... ]
}
```

## GraphiQL
 - 개발단계에서 디버깅 시 많은 도움이 되지만, 운영 환경에서는 반드시 비활성화 해야한다.

