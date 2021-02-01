# Data Transfer Object
- **메서드 간의 호출 수**를 **감소**하기 위해 프로세스간 데이터를 전달하는 객체

<br>

- 비용이 많이 드는 호출!
  - 원격 인터페이스로 호출 할 때마다 비용이 많이 든다.
  - 그러므로 호출 수를 줄여아하는 상황이 된다.
  - 호출 수를 줄이자니, 전송해야하는 데이터 양이 많아진다.
  - 해결방법으로 매개 변수를 이용하는 방법도 있지만 프로그램이 어색해지기도 하고, JAVA에서는 불가능한 방법이다.
  
<br>

- 그래서 어떤 해결책이 있을까?
  - 호출에 대한 모든 데이터를 보관할 수 있는 Data Transfer Object를 만드는 것!
  
  <br>
  
- DTO(Data Transfer Object)의 장점!
  - 여러 원격 호출을 단일 호출로 일괄 처리
  - 직렬화 매커니즘을 캡슐화(이게 무슨 말일까...?)
  
  <br>
  
[참고](https://martinfowler.com/eaaCatalog/dataTransferObject.html)
