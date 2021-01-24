
# Rest API GET 목록조회 

오늘은 Spring Boot를 이용해서 REST Api Get Method를 구현했다. 
하나는 사용자 목록 조회하는 것이고, 하나는 사용자 아이디로 조회하는 것이다.
사실 사용자 아이디로 조회하는건 잘 안되는것 같다.
그건 내일 해결해봐야겠다. 😂 
### 목록조회
UserController.java

```java
@RestController
public class UserController {
    public final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/api/users")
    public ApiResult<List<UserDto>> getUsers() {
        return OK(
                userService.findAllUser().stream()
                        .map(UserDto::new)
                        .collect(toList())
        );
    }
}
```

우선 컨트롤러를 작성했다. `return`의 `OK`는 `ApiResult`에 작성했다.

<br>
ApiResult.java

```java
public class ApiResult<T> {
    private final boolean success;

    private final T response;

    private final ApiError error;

    public ApiResult(boolean success, T response, ApiError error) {
        this.success = success;
        this.response = response;
        this.error = error;
    }

    public static <T> ApiResult<T> OK(T response) {
        return new ApiResult<>(true, response, null);
    }

    public static ApiResult<?> ERROR(Throwable throwable, HttpStatus status) {
        return new ApiResult<>(false, null, new ApiError(throwable, status));
    }

    public static ApiResult<?> ERROR(String errorMessage, HttpStatus status) {
        return new ApiResult<>(false, null, new ApiError(errorMessage, status));
    }

    public boolean isSuccess() {
        return success;
    }

    public T getResponse() {
        return response;
    }

    public ApiError getError() {
        return error;
    }

    @Override
    public String toString() {
        return new ToStringBuilder(this, ToStringStyle.SHORT_PREFIX_STYLE)
                .append("success", success)
                .append("response", response)
                .append("error", error)
                .toString();
    }
}
```

ApiResult가 해주는 역할은 결과값을 전달해주는 형식을 정해준다. 
사실 이 코드가 내 코드라기보다는 지금 하는 스터디에서 본 코드라서,, 내 코드라고 하기에는 양심에 매우매우 찔린다.😊
`OK`가 되면 `true`값과 함께 `response`를 반환한다.
`ERROR`이라면 `false`값과 함께 에러 내용을 반환한다.
그냥 바로 값을 return하는 것보다 저렇게 에러 메세지를 보내주면 확실히 더 좋을 듯하다. 기억해야지.👍

여기서 내가 유심히 본 것은 `ToStringBuilder`이다. 
저게 뭔가해서 검색해보니 `.toString()`의 단점을 보안해주는 것이라한다.
`.toString()`를 쓰면 굉장히 노가다를 요구하는데, `ToStringBuilder`는 그걸 해결해준다.
`SHORT_PREFIX_STYLE`라는 형식을 지정해주는 것처럼 생겼다. 아마도 맞을 것이다.ㅎ😜
저거 말고도 다른 것도 있던데 필요할 때 쓰면 되겠다. 근데 주로 `SHORT_PREFIX_STYLE`를 쓰는 듯하다.

저기에 보면 `ApiError`가 있다.

<br>
ApiError.java

```java
public class ApiError {
    private final String message;

    private final int status;

    ApiError(Throwable throwable, HttpStatus status) {
        this(throwable.getMessage(), status);
    }

    public ApiError(String message, HttpStatus status) {
        this.message = message;
        this.status = status.value();
    }


    public String getMessage() {
        return message;
    }

    public int getStatus() {
        return status;
    }

    @Override
    public String toString() {
    return new ToStringBuilder(this, ToStringStyle.SHORT_PREFIX_STYLE)
            .append("message", message)
            .append("status", status)
            .toString();

    }
}
```

에러를 처리해주는 클래스다. 보면 쉽게 이해가 될 만한 코드다.
에러가 되면 상태값과 메세지를 반화해준다.
<br>

UserService.java
```java
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Transactional(readOnly = true)
    public List<User> findAllUser() {
        return userRepository.findAllUser();
    }
  }
```

다시 컨트롤러로 가면 컨트롤러에서 서비스를 호출한다.
저  `@Transactional` 저 친구가 의문이다.🤔
오늘은 시간이 늦었으니 내일 알아봐야겠다. 대충 수정이 불가능하게 만들어라 이런 느낌이다.
보통 서비스에서 `Repository`를 호출한다. 여기서 null 값을 체크한다거나 하는 로직이 들어갈 수도 있겠다고 생각한다.🤔

<br>

JdbcUserRepository.java
```java
@Repository
public class JdbcUserRepository implements UserRepository {

    private JdbcTemplate template;

    public JdbcUserRepository(JdbcTemplate template) {
        this.template = template;
    }


    private static final Logger LOGGER = LoggerFactory.getLogger(JdbcUserRepository.class);

    public List<User> findAllUser() {
        return template.query("SELECT * FROM users",
                (rs, rowNum) -> new User(
                        rs.getLong("seq"),
                        new Email(rs.getString("email")),
                        rs.getString("passwd")
                )
        );
    }
}
```

`JdbcUserRepository`에 오기전에 `UserRepository`라는 인터페이스를 만들어서 한번 거쳐서 오게 만들었다.
인터페이스틑 왜 만드는지 찾아본 결과 **협업시 유리**하게 하기 위해서라고 한다. 이걸로는 내 호기심이 채워지지는 않았으니 다음에 더 알아보기로 한다.
여기서는 `JdbcTemplate`을 사용했다.
H2 DB에 저장된 데이터들을 불러와야한다. 와 여기서 시간을 엄청 썼다ㅠㅠ😭😭 진짜 이게 뭐라고 흑흑
저기에 쿼리문을 따로 빼서 관리하기도 한다는 것 같은데 나는 매우 작은 프로젝트므로 그냥 했다.

저기에 있는  `LoggerFactory`도 원래 그냥 썼던건데 새삼 저 친구도 궁금해졌다.
공부를 하면 할 수록 내가 모르는 것들이 명확해지고 그럼으로써 더 공부의 필요성을 느끼는 것 같다.

여기까지 하면 로직은 끝났다. 다음은 DTO다.
<br>
UserDto.java

```java
public class UserDto {
    private Long seq; // 불변
    private Email email; // 입력
    private String password; // 입력
    private int loginCount; // 가변
    private LocalDateTime lastLoginAt; // 가변
    private LocalDateTime createAt; // 불변

    public UserDto(User source) {
        copyProperties(source, this);
    }

    public Long getSeq() {
        return seq;
    }

    public void setSeq(Long seq) {
        this.seq = seq;
    }

    public Email getEmail() {
        return email;
    }

    public void setEmail(Email email) {
        this.email = email;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public int getLoginCount() {
        return loginCount;
    }

    public void setLoginCount(int loginCount) {
        this.loginCount = loginCount;
    }

    public LocalDateTime getLastLoginAt() {
        return lastLoginAt;
    }

    public void setLastLoginAt(LocalDateTime lastLoginAt) {
        this.lastLoginAt = lastLoginAt;
    }

    public LocalDateTime getCreateAt() {
        return createAt;
    }

    public void setCreateAt(LocalDateTime createAt) {
        this.createAt = createAt;
    }

    @Override
    public String toString() {
        return new ToStringBuilder(this, ToStringStyle.SHORT_PREFIX_STYLE)
                .append("seq", seq)
                .append("email", email)
                .append("password", password)
                .append("loginCount", loginCount)
                .append("lastLoginAt", lastLoginAt)
                .append("createAt", createAt)
                .toString();
    }
}
```

이 밖에도 H2 Database 연동도 힘들었고, maven 설정에도 애를 엄청나게 먹었다.
회사에서는 이미 되어 있는 것들을 하니까 새삼 몰랐나보다.
이제 알았으며 됐다.😁
테스트는 Postman으로 했다. 

GOOD!
