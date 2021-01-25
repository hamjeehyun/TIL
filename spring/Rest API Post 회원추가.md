
# Rest API POST 회원추가

> POST API로 회원추가하기

UserController.java

```java
@PostMapping("/api/users/join")
public ApiResult<JoinResult> getUser(@RequestBody JoinRequest joinRequest) {
    User user = userService.join(new Email(joinRequest.getPrincipal()), joinRequest.getCredentials());
    return OK(
            new JoinResult(new UserDto(user))
    );
}
```

우선 UserController부터 살펴보겠다. `RequestBody`로 `JoinRequest` DTO를 보내주고, `JoinResult`로 return 한다. DTO 구조는 마지막에 살펴보기로 하고 userService로 넘어가겠다. 😎


UserService.java

```java
@Transactional
   public User join(Email email, String password) {
       checkArgument(isNotEmpty(password), "password must be provided");
       checkArgument(
               password.length() >= 4 && password.length() <= 15,
               "password length must be between 4 and 15 characters."
       );

       User user = new User(email,password);
       return insert(user);
   }

   private User insert(User user) {
       return userRepository.insert(user);
   }
```

내가 참고한 코드에서는 `User user = new User(email, passwordEncoder.encode(password));`로 되어있다. 그런데 내가 뭘 잘못한 것인지 `passwordEncoder`에서 자꾸 오류가 걸린다. 아마 다음에 하게 될 jwt와 연관되어 있는 듯하다. 
<br>
여기서는 password의 길이 정도와 이메일과 password의 null체크만 해주고 userRepository를 부른다.

UserRepository.java

```java
@Override
public User insert(User user) {
    KeyHolder keyHolder = new GeneratedKeyHolder();
    template.update(connection -> {
        PreparedStatement preparedStatement = connection.prepareStatement(
                "INSERT INTO users(seq,email,passwd,login_count,last_login_at,create_at) VALUES (null,?,?,?,?,?)", new String[]{"seq"});
        preparedStatement.setString(1, user.getEmail().getAddress());
        preparedStatement.setString(2, user.getPassword());
        preparedStatement.setInt(3, user.getLoginCount());
        preparedStatement.setTimestamp(4, timeStampOf(user.getLastLoginAt().orElse(null)));
        preparedStatement.setTimestamp(5, timeStampOf(user.getCreateAt()));
        return preparedStatement;
    }, keyHolder);

    Number key = keyHolder.getKey();
    long generatedSeq = key != null ? key.longValue() : -1;

    return new User.Builder(user)
            .seq(generatedSeq)
            .build();
}
```

솔직히 말하자면 여기 코드는 100% 다 이해하지 못했다.😥 우선 `KeyHolder`가 무엇인지 모른다. 또한 `PreparedStatement`의 역할도 모른다. 그냥 따라 적은거라 보면 된다. 😫💦💦 

JoinRequest.java

```java
public class JoinRequest {
    private String principal;

    private String credentials;

    protected JoinRequest() {}

    public String getPrincipal() {
        return principal;
    }

    public String getCredentials() {
        return credentials;
    }

    @Override
    public String toString() {
        return new ToStringBuilder(this, ToStringStyle.SHORT_PREFIX_STYLE)
                .append("principal", principal)
                .append("credentials", credentials)
                .toString();
    }
}

```
<br>
JoinResult.java

```java
public class JoinResult {

    private final UserDto user;

    public JoinResult(UserDto user) {
        checkNotNull(user, "user must be provided");
        this.user = user;
    }

    public UserDto getUser() {
        return user;
    }

    @Override
    public String toString() {
        return new ToStringBuilder(this, ToStringStyle.SHORT_PREFIX_STYLE)
                .append("user", user)
                .toString();
    }
}
```
<br>
<br>
오늘 공부는 헛으로 한 것 같다. 그래도 스터디 1주차 숙제는 끝이 났다. 그러나 이것들이 전부 내 것이 되었냐고 물어본다면......... <br>
나는 이 스터디에서 얻어가는 것은 자세한 내용들이 아니라고 생각한다. 나 혼자서 기술셋을 잡고 그것을 공부해나가기에 한계를 느꼈기 때문에 신청한 스터디이다. 그런 의미에서 이 스터디는 좋은 길라잡이가 된다.🎈
<br>
내일은 jwt에 대해 공부할 것이다.
<br>
화이팅
