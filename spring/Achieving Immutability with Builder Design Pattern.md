# Achieving Immutability with Builder Design Pattern


- 클래스는 불변하게 만들어야한다.
  - 불변하게 만들 수 없다면 최대한 불변할 수 있도록 만들어야한다.
  - 모든 상태정보는 객체가 생성될 때 만들어지며 그것은 개체 수명동안 바뀌지 않는다.



- 왜 불변 클래스를 써야만 할까?

  - 장점

    - 불변객체는 만들어질 때 생성된 상태 하나만을 가지며, 이것은 좀더 간단하고 쉽게 쓸 수 있다.

    - 본질적으로 쓰레드로부터 안전하다. 즉, 동기화가 필요하지 않다.(무슨말이지?)

    - 불변 클래스의 객체는 자유롭게 공유 가능하다. 

      - 예를들어, Boolean 클래스는 기존의 TRUE, FALSE 인스턴스를 재사용하고, Boolean.valueOf 메서드를 호출 할 떄마다 이미 만들어진 인스턴스를 제공한다. (이게 저거랑 무슨 상관인데!ㅠ)

      

- 불변 클래스 만들기

  ```
  public final class User {
      private final String username;
      private final String password;
  
      public User(String username, String password) {
          this.username = username;
          this.password = password;
      }
  
      public String getUsername() {
          return username;	
      }
  
      public String getPassword() {
          return password;
      }
  }
  ```

  - 이 클래스가 불변인 이유:
    1. setter methods or mutators.를 제공하지 않는다.
    2. 이 클래스는 final로 인해 확장 될 수 없다. 이는 private 생성자를 만들어 수행할 수 있다.
    3. 클래스의 필드는 모두 final과 private 이다.



- 실제 Application에서는 위에 보다 많은 Field가 들어간다. 좀 더 실제적인 클래스는 다음과 같다.

  ```
  public final class User {
      private final String username;
      private final String password;
      private String firstname;
      private String lastname;
      private String email;
      private Date creationDate;
  
      public User(String username, String password) {
          this.username = username;
          this.password = password;
          creationDate = new Date();
      }
  
      public String getUsername() {
          return username;
      }
  
      public String getPassword() {
          return password;
      }
  
      public String getFirstname() {
          return firstname;
      }
  
      public void setFirstname(String firstname) {
          this.firstname = firstname;
      }
  
      public String getLastname() {
          return lastname;
      }
  
      public void setLastname(String lastname) {
          this.lastname = lastname;
      }
  
      public String getEmail() {
          return email;
      }
  
      public void setEmail(String email) {
          this.email = email;
      }
  
      public Date getCreationDate() {
          return new Date(creationDate.getTime());
      }
  }
  ```

  - 이 클래스는mutator(즉, setter)가 있기 때문에  불변이 아니다.  이 클래스는 만들어진 이후에도 수정이 가능하다.  

  - 단점

    - 객체는 구성과정에서 일관성이 없는 상태일 수 있다.
    - 쓰레드 안정성에서 추가적인 노력이 필요하다.

    

- **Builder Design Pattern comes to the Rescue**
// TODO 마저 읽기

https://dzone.com/articles/immutability-with-builder-design-pattern
