# Queries and Mutations

Reference: https://graphql-kr.github.io/learn/queries/

1. Fields (필드)
2. Arguments (인자)
3. Aliases (별칭)
4. Fragments (프래그먼트)
5. Operation Name (작업 이름)
6. Variables (변수)
7. Directives (지시어)
8. Mutations (뮤테이션)
9. Inline Fragments (인라인 프래그먼트)

## 1. 필드

- ![](https://i.imgur.com/afUBkV0.png)
  - `name` 필드는 `String` 타입을 반환하고 있다.
- 쿼리(좌)와 결과(우)가 동일하다. 따라서 항상 기대한 결과를 얻을 수 있다.

- 객체인 필드에 하위 선택(sub-selection)을 할 수 있다
- ![](https://i.imgur.com/7EzjXfq.png)
  - friends는 객체로 이루어진 배열
- ![](https://i.imgur.com/I14TReG.png)

## 2. 인자

- 필드에 인자를 전달
- ![](https://i.imgur.com/qH3tVU3.png)
  - ![](https://i.imgur.com/4TK6JYL.png)
  - `human` 필드는 `id`가 필수다.
- GraphQL에서 모든 필드와 중첩된 객체가 인자를 가질 수 있다.
  - REST와의 차이: 쿼리 파라미터나 URL 세그먼트 같은 단일 인자만 전달할 수 있다.
- ![](https://i.imgur.com/kt6q4G3.png)
  - ex) `height(unit: METER)`: `METER`나 `FOOT` 중 하나를 가질 수 있다.
  - `Enumeration` 열거형 타입을 사용 (뒤에 스키마 & 타입에서 더 자세히 다룬다.)
- ![](https://i.imgur.com/vU5aCrv.png)
  - 별칭으로 이 문제를 해결할 수 있다.

## 3. 별칭

- 위 사진처럼 다른 인자를 사용하며 같은 필드를 쿼리할 수가 없다.
- 별칭: 필드의 결과를 원하는 이름으로 바꿀 수 있는 기능이다.
- ![](https://i.imgur.com/UKXIAfk.png)
  - 둘 다 `hero` 필드를 사용하지만, 별칭을 지정하며 충돌을 피한다

## 4. 프래그먼트

- 프래그먼트: 재사용 가능한 단위
- ![](https://i.imgur.com/qroSlxy.png)
- 변수를 넣어서 사용
  - ![](https://i.imgur.com/zrFAeAv.png)

## 5. 작업 이름

- 앞서 나온 예시에서는 `query` 키워드를 사용한 예제와 사용하지 않은 예제가 모두 있었다.
- ex) 작업 타입: `query`, 작업 이름: `HeroNameAndFriends`
  - ![](https://i.imgur.com/8dTIQQY.png)
- 작업 타입: `query`, `mutation`, `subscription`
- 작업 이름: 의미있고 명시적인 작업의 이름
  - 함수에 이름을 부여하면 코드 디버깅 했을 때 편하다.

## 6. 변수

- 쿼리 문자열을 런타임에 동적으로 조작할 수 있도록 변수를 사용한다.
- 변수를 사용하기 위한 작업
  1. 정적 값을 `$variableName`으로 변경한다
  2. `$variableName`을 쿼리에서 받는 변수로 선언한다
  3. 전송규약(주로 JSON) 변수에 `variableName: value`를 전달한다
- ![](https://i.imgur.com/TyE5V0l.png)
- 변수 정의: `$episode: Episode`
  - 변수는 `스칼라`, `열거형`, `input object type` 중 하나여야 한다
  - 변수 정의는 옵셔널/필수 선택
    - 필수일 경우 타입 옆에 `!` 추가
  - 변수 기본값 설정
    - `$episode: Episode = "JEDI"`

## 7. 지시어

- 변수를 사용하여 쿼리의 구조와 형태를 동적으로 변경하는 방법도 필요하다.
- ![](https://i.imgur.com/cBlCT92.png)
- ![](https://i.imgur.com/vRt2fbF.png)
- 지시어는 필드나 프래그먼트 안에 삽입할 수 있다

- 코어 GraphQL 지시어:
  - `@include(if: Boolean)`: `true`일 때만 필드를 포함
  - `@skip(if: Boolean)`: `true`일 때 건너뜀

## 8. 뮤테이션

- 데이터 fetch 말고 서버 측에서 데이터 수정이 필요할 경우
- REST: GET 요청으로 데이터 수정을 하지 않는 규칙이 있다.
- GraphQL: 기술적으로는 가능하나, 변경을 발생시키는 작업은 뮤테이션을 통해 한다는 규칙이 있다.

- ![](https://i.imgur.com/CYdvVWs.png)
  ```graphql
  mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
    createReview(episode: $ep, review: $review) {
      stars
      commentary
    }
  }
  ```
  - 뮤테이션 필드가 객체를 반환하면 중첩 필드를 요청할 수 있다.
    - 새로운 상태를 가져올 때 사용한다.
    - 하나의 요청으로 값을 변경하며 쿼리를 할 수 있는 장점이 있다.
- 쿼리와 뮤테이션의 차이점
  - 쿼리 필드는 **병렬**로 실행
  - 뮤테이션 필드는 **하나씩 차례대로** 실행

## 9. 인라인 프래그먼트

- **인터페이스**나 **유니언** 타입을 반환하는 필드를 쿼리할 때는 **인라인 프래그먼트** 을 사용해야한다.
- ![](https://i.imgur.com/jRWTaLo.png)
- ![](https://i.imgur.com/PNetQuV.png)
- 메타 필드
  - ex) `__typename`: 객체 타입의 이름을 얻을 수 있다
  - ![](https://i.imgur.com/vdPlQ0j.png)
