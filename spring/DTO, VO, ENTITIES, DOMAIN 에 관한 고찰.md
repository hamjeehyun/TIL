## DTO, VO, ENTITIES, DOMAIN 에 관한 고찰



1. Entities
   1. 변경이 가능하다.
   2. Attribute가 아닌 ID 로 식별한다.
      1. Attribute가 다르더라도 ID가 같으면 같은  Entity 로 분류한다. 이것은 언제라도 Attribute가 변경될 수 있음을 뜻한다.
2. Value objects
   1. 변경이 불가능하다.
   2. Entity와 다르게 ID가 없다.
      1. Type와 Attribute가 같다면 같은 것으로 간주된다.
   3. 종종 메세지 전달의 용도로 쓰인다.
      1. useful in the API layer within an onion architecture (양파구조...?)
   4. VO의 이점
      1. The compound of value objects can swallow lots of computational complexity.(이게 무슨 말일까? 많은 속성을 가지고 있는 VO는 복잡성, 그러니까 로직이 들어갈 수 있다는 것을 뜻하는 것인지)
      2. Entities can be released from logic complexity. (Entity를 logic complexity에서 해방시킨다... 무슨 의미인지 모르겠다.)
      3. 테스트 가능성, 동시성에 대한 확장성
3. Aggregate roots
   1. 다른 Entity를 묶어주는 Entity 이다.
   2. Aggregate roots는 aggregate 의 일부분이다. 
      1. 각 Aggregate 는 root와  boundary 로 구성된다.
         1.  For example, the relationship between Order and OrderLineItem within SalesOrderDomain can be considered as an aggregate where Order acts as the aggregate root, while the OrderLineItem is the child of Order within SalesOrder boundary. 
   3. 외부 객체는 Aggregate 의 하위 Entity를 참조할 수 없다.
      1. 따라서, Aggregate root의 하위 Entity에 접근하고 싶다면, Aggregate  root를 통해야 한다. 이것은 하위 항목에 직접적으로 접근 할 수 없다는 것을 뜻한다.
   4. 다른 도메인을 갖는 기능들은 **가능한 한**, Aggregate  root를 통하면 더욱 좋다.
4. Repositories
   1. 저장소를 처리하는데 사용한다.
   2.  Repository는 file-based storage또는 DB(SQL/ No-SQL), caching과 같은 저장 매커니즘이다.
   3. Repository는 Aggregate root를 저장하는 것이다.
      1. Repository 구현은 Aggregate 구조를 갖기위해 다른 위치의 여러 저장소와 통신해야한다. (이게 뭔말이야)
      2. Thus, a single aggregate root might be drawn from a REST API, as well as a database or files.
      3. I implement the repository as an interface within the domain/domain services layer within onion architecture, and then the implementation logic of the repository interface is going to be defined in the infrastructure layer
5. Factories
   1. 객체의 추상화를 제공한다.
   2. Factory는 Aggregate root 또는 Entity이나 VO를 return 한다.
   3. Usually, factories also implemented as an interface within the domain/domain services layer with the implementation logic will be defined in the infrastructure layer.
6. Services
   1. Aggregate root에 맞지 않는 기능을 제공하기 위해 존재한다.

![img](https://cdn-images-1.medium.com/max/1600/1*-PJWpAFq29uDEqJt0zMrDQ.png)



![img](https://cdn-images-1.medium.com/max/1600/1*gLLWywd76Tg_poJ0aiWalg.png)



[참고한 것](https://dzone.com/articles/ddd-part-ii-ddd-building-blocks)



- 참고해야할 것

https://martinfowler.com/eaaCatalog/dataTransferObject.html

https://martinfowler.com/bliki/ValueObject.html

https://multifrontgarden.tistory.com/182





- `BeanUtils.copyProperties(source, target);`
  - source : 원본 객체
  - target : 복사 대상 객체

- `BeanUtils.copyProperties(source, target, String ... ignoreProperites);`

  - source : 원본 객체

  - target : 복사 대상 객체

  - ignoreProperities : 복사를 원하지 않는 프로퍼티명

출처: https://junghn.tistory.com/entry/SPRING-BeanUtilscopyProperties을-이용하여-Class간의-property-복사하기 [코딩 시그널]





- `JsonUtil.toJson()`
- `toJsonString()`

위에 두 개는 결국 같은 기능을 수행한다.

