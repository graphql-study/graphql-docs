별칭 
눈썰미가 좋으신 분들은 알아차리셨겠지만, 결과 객체 필드가 ​​쿼리의 필드 이름과 일치하지만 인자는 그렇지 않으므로 다른 인자를 사용하여 같은 필드를 직접 쿼리 할 수는 없습니다. 그렇기 때문에 필드의 결과를 원하는 이름으로 바꿀 수 있습니다. 이 것이 별칭 이 필요한 이유입니다.

{
  empireHero: hero(episode: EMPIRE) {
    name
  }
  jediHero: hero(episode: JEDI) {
    name
  }
}
{
  "data": {
    "empireHero": {
      "name": "Luke Skywalker"
    },
    "jediHero": {
      "name": "R2-D2"
    }
  }
}
위 예제에서 두 hero 필드는 서로 충돌하지만, 서로 다른 이름의 별칭을 지정할 수 있으므로 한 요청에서 두 결과를 모두 얻을 수 있습니다.



## dataloader vs mongoose populate
https://stackoverflow.com/a/52669965
