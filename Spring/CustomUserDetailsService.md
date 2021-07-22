# UserDetailsService 원하는 방식으로 구현하기

> Spring Security를 사용하면서 단순히 아이디, 비밀번호, 권한 정보 외에도 추가적인 정보를 세션에 저장하고 있어야 하는 상황이 있다. 이러한 경우에는 UserDetailsService와 UserDetails(혹은 User) 인터페이스를 구현하여 원하는 방식으로 변형해야 할 것이다.
>
> 여기서는 간단하게 UserDetailsService 인터페이스의 구조를 살펴보고 이를 커스텀하여 적용하는 과정을 정리해보고자 한다.

.



### Spring Security의 인증 절차

![img](https://blog.kakaocdn.net/dn/ceanmM/btqYIkl9GF0/3AKGUzcpXgrHg1hOFOvNz0/img.png)



- UserDetailsService와 UserDetails의 동작구조에 주목하여, 인증 후 세션에 저장될 사용자 정보 요소를 원하는 방식으로 수정
- 우선 __`AuthenticationFilter`__ 는 정상적으로 인증이 이루어졌다면 최종적으로 생성된 __`User`__ 객체를 __`SecurityContextHolder`__에 보관하게 됨
- __`User`__ 객체는 __`UserDetails`__ 인터페이스를 구현한 객체가 되며, 이는 __`UserDetailsService`__ 를 거쳐 필터로 전달되는 것
- 따라서, 당연한 얘기겠지만 __`UserDetails`__ 의 인스턴스로 원하는 정보를 넣으면, 결과적으로 이것이 세션에 저장되는 것 

.

### UserDetailsService 구현하기

- 아래 예시에서는 사용자가 입력한 username을 기반으로 DB에서 일치하는 사용자 정보가 있는 지를 조회

- 사용자 정보가 조회하면 이를 기반으로 __`UserDetails`__ 를 구현한 객체를 만들어 리턴

- 리턴된 객체는 이후 인증 필터를 거치면서 사용자가 입력한 비밀번호와 일치하는 지 검증하여 최종 로그인 성공여부가 결정되는 것

  - 당연히 비밀번호 검증시에는 bean으로 선언한 암호화 클래스가 사용될 것!

  #### 

```java
@Component
public class CustomUserDetailsService implements UserDetailsService{
    
    //임의로 선언한 @Repository bean
    private UserRepository userRepository;
    
    @Autowired
    public CustomuserDetailsService(UserRepository userRepository){
        this.userRepository = userRepository;
    }
    
    ...
    
    @Override
    public UserDetails loaduserByUsername(String username) throws UsernameNotFoundException{
        
        //DB로부터 입력받은 username에 해당하는 사용자 정보를 불러들임
        UserEntity userEntity = userRepository.getUserinfo(username);
        
        //사용자 정보가 없을 경우 예외처리
        if(userEntity == null){
            throw new UsernaeNotFoundException("No such user exists.");
        }
        
        //UserDetails를 구현한 CustomUserDetails 클래스에 사용자 정보를 담아 리턴
       	CustomuserDetails customUserDetails = new CustomUserDetails();
        customUserDetails.setUsername(userEntity.getUserId());
        customUserDetails.setPassword(userEntity.getPassword());
        
        List<GrantedAuthority> authorities = new ArrayList<>();
        authorities.add(new SimpleGrantedAuthority(customUserDetails.getUserRole()));
        customUserDetails.setAuthorities(authorities);
        
        return customUserDetails;
    }
    
}
```



### UserDetails 인터페이스 구현

- 위의 예시에서 __`loaduserByUsername()`__ 에 의해 반환되는 사용자 정보가 담긴 객체

- 앞서 얘기한대로 아이디, 비밀번호, 인가 관련 정보 외에도 원하는 사용자 정보를 인스턴스로 담아 반환할 수 있음

  - 위의 __`CustomUserDetailsService`__ 에서 원하는 인스턴스에 값을 할당

  (lombok 사용을 가정했기 때문에 따로 setter, getter은 명시하지 않았다.)

  ```java
  @Getter
  @Setter
  @ToString
  public class CustomUserDetails implements UserDetails{
      
  	private String username;
  	private String password;
  	private String additionalInfo1; //세션에 추가할 정보 인스턴스
  	private String additionalInfo2; //세션에 추가할 정보 인스턴스
  	private Collection<? extends GrantedAuthority> authorities;
  
  	@Override
  	public String getUsername() {
          return username;
  	}
  
  	@Override
  	public String getPassword() {
      	return password;
  	}
  
  	@Override
  	public boolean isEnabled() {
          return isEnabled;
      }
  
      @Override
      public boolean isAccountNonExpired() {
          return true;
      }
  
      @Override
      public boolean isAccountNonLocked() {
          return true;
      }
  
      @Override
      public boolean isCredentialsNonExpired() {
          return true;
      }
  
      @Override
      public Collection<? extends GrantedAuthority> getAuthorities() {
          return authorities;
      }
  }
  ```

  .

  ### SecurityConfig에 CustomUserDetailsService 등록

  ```java
  @Configuration
  @EnableWebSecurity
  public class SecurityConfig extends WebSecurityConfigurerAdapter{
  	
      private CustomUserDetailsService customUserDetailsService;
      
      @Autowired
      public SecurityConfig(CustomUserDetailsService customUserDetailsService){
          this.customUserDetailsService = customUserDetailsService;
      }
      
      ...
      @Override
      protected void configure(AuthenticationManagerBuilder auth) throws Exception{
          //CustomUserDetailsService를 등록
      	auth.userDetailsService(customUserDetailsService);    
      }
      
  }
  ```

  
