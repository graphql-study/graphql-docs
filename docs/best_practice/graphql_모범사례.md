# Graphql 모범 사례

## Http를 통한 서빙

- web request pipeline
  - graphql 레이어는 (filter, plugin 담당) middleware 뒤쪽에서 동작함
  - 즉 모든 인증 미들웨어 뒤에 있어야함
  - 이는 http 엔드포인터 핸들러(controller 레벨)에서 동일한 session, user info에 접근가능합니다.
- uris, routes
  - Graphql 서버는 단일 url/endpoint에서 동작합니다.

### Http
> `GET`, `POST`를 핸들링

#### GET
HTTP GET 요청을 받게되면 Graphql 서버는 query 파라미터로 이를 전달 받게 됩니다.

```
{
  user {
    name
  }
}
```

다음의 쿼리는 아래 http GET으로 변환 될 수 있습니다.

```
https://myapi/graphql?query={user{name}}
```

`operationName`이라함은 아래에서 `getDogName`, `getOwnerName`을 의미한다.

```
query getDogName {
  dog {
    name
  }
}

query getOwnerName {
  dog {
    owner {
      name
    }
  }
}
```

실제 구현되었던 graphql http 요청 예시 (`GET`)

```json
{
  "operationName":"placesAndSpotsByKeyword",
  "variables":{
    "query":"dd",
    "keyword":"dd",
    "X":127.06970349999999,
    "Y":37.297542299999996,
    "currentPage":1},
  "query":
  "query placesAndSpotsByKeyword($query: String!, $keyword: String!, $X: Float, $Y: Float, $currentPage: Int) {
    places(filters: {query: $query, x: $X, y: $Y, page: $currentPage, size: 10}) {
      pageInfo {
        total_count
        is_end
        cur_page
        total_page_count
        __typename
      }
      places {
        id
        place_name
        category_name
        category_group_name
        address_name
        road_address_name
        x
        y
        __typename
      }
      __typename
    }
    spots(searchSpotDto: {keyword: $keyword, x: $X, y: $Y}) {
      _id
      place_name
      category_name
      category_group_name
      address_name
      road_address_name
      x
      y
      __typename
    }
  }"
}
```

- `operationName`
  - 서버에 정의된 리솔버 `placesAndSpotsByKeyword`를 호출 
- `variables`
  - 아래 쿼리에 활용될 변수들을 key: value로 지정
- `query` 
  - 서버에 정의된 스키마에 따라 string 타입의 쿼리를 보냄


##