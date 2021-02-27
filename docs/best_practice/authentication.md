# Graphql 모범 사례(authentication)


> 인증 로직을 비즈니스 로직 레이어에 위임하라

- repository, model 코드로 위임하라는 뜻 같다.

## 문제상황

```js
var postType = new GraphQLObjectType({
  name: ‘Post’,
  fields: {
    body: {
      type: GraphQLString,
      resolve: (post, args, context, { rootValue }) => {
        // return the post body only if the user is the post's author
        if (context.user && (context.user.id === post.authorId)) {
          return post.body;
        }
        return null;
      }
    }
  }
});
```

- 위 코드의 문제점
  - 이 코드를 서비스의 각 엔드포인트에 복사해야함(중복)
  - 만약 이 경우, 복사된 모든 인증 로직이 완벽하게 일치하지 않는 경우, 사용자는 **사용하는 API에 따라 다른 데이터를 보게될 수도 있다.**.


## 해결책

```js
// postRepository에게 인증 코드 위임
var postRepository = require('postRepository');

var postType = new GraphQLObjectType({
  name: ‘Post’,
  fields: {
    body: {
      type: GraphQLString,
      resolve: (post, args, context, { rootValue }) => {
        return postRepository.getBody(context.user, post); // 실제 인증 코드 동작
      }
    }
  }
});
```

이를 해결하기 위해서 **비즈니스 로직 레이어에 인증 로직을 위임**하여, `단일 소스(single source of truth) 인증`을 실현시킵니다.

## real code example (3)
> [apollo-server](https://www.apollographql.com/docs/apollo-server/security/authentication/)


### 기본 서버 환경 (token level)
> requeset별 context에 유저정보 넣어주기

- http 헤더의 req.headers.authorization token을 읽어, 유저를 검증한다.
- 이후 context에 user 정보를 넣어준다. 

```ts
// using apollo-server 2.x
const { ApolloServer } = require('apollo-server');

const server = new ApolloServer({
 typeDefs,
 resolvers,
 context: ({ req }) => {
   const token = req.headers.authorization || '';

   // try to retrieve a user with the token
   // 구현 필요함
   const user = getUser(token);

   // add the user to the context
   return { user };
 },
});

server.listen().then(({ url }) => {
 console.log(`🚀 Server ready at ${url}`)
});

```

- The context object is one that gets passed to every single resolver at every level, **so we can access it anywhere in our schema code.**
- Since the **context is generated again with every new request**, we don’t have to worry about cleaning up user data at the end of execution.

### 1. Schema authorization
> `all or nothing` strategy

모든 필드를 private으로 전환하여, 권한이 있어야만 필드를 볼 수 있다. (기본 환경에 사용된 방식)

- 장점: simplest
- 단점: 필드별 권한을 줄 수 없다.


```ts
context: ({ req }) => {
 // get the user token from the headers
 const token = req.headers.authorization || '';

 // try to retrieve a user with the token
 const user = getUser(token);

 // optionally block the user
 // we could also check user roles/permissions here
 if (!user) throw new AuthenticationError('you must be logged in'); 

 // add the user to the context
 return { user };
},
```


### 2. Authorization in resolvers
> 각 resolver(필드) 마다, 권한 검사


```ts
users: (parent, args, context) => {
 if (!context.user) return null;

 return ['bob', 'jake'];
}
```

- 유저의 권한 검사를 포함한 코드
  - 이를 통해 권한 별로, 필드를 public / private으로 돌릴 수 있다.
```ts
users: (parent, args, context) => {
 if (!context.user || !context.user.roles.includes('admin')) return null;
 return context.models.User.getAll();
}
```


### 3. **Authorization in data models**

- **공식문서에서 추천해준 방식** (centralized Model objects that each represent a concept from your application)
- 쿼리(스키마) 별로, 공통적으로 쓰이는 필드가 있다면, 해당 방식으로 관리 가능하다.


- entity를 관리하는 repository 레벨에 필드 접근 메서드 정의
```ts
export const generateUserModel = ({ user }) => ({
 getAll: () => { /* fetching/transform logic for all users */ },
 getById: (id) => { /* fetching/transform logic for a single user */ },
 getByGroupId: (id) => { /* fetching/transform logic for a group of users */ },
});
```

- DI repository to context level(app level)

```ts
context: ({ req }) => {
 // get the user token from the headers
 const token = req.headers.authentication || '';
  
 // try to retrieve a user with the token
 const user = getUser(token);

 // optionally block the user
 // we could also check user roles/permissions here
 if (!user) throw new AuthenticationError('you must be logged in to query this schema');  

 // add the user to the context
 return {
   user,
   models: {
     User: generateUserModel({ user }),
     ...
   }
 };
},
```


