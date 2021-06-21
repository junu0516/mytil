### Spring Security의 동작구조

- 필터(Filter)를 기반으로 동작하며, Spring MVC와는 분리되어 관리됨
- 클라이언트의 요청이 DispatcherServlet으로 오기전에 Spring Security Filter Chain을 거쳐, 적절한 인증 및 인가를 진행
- ``UsernamePasswordAuthenticationFilter`` : <form> 태그 기반의 인증을 담당하며, 로그인 URL로 오는 요청을 처리하고 인증을 진행함
  - ``AuthenticationManager``을 통해 인증이 실행
  - 인증에 성공하면, ``Authentication``객체를 ``SecurityContext``에 저장한 후, ``AuthenticationSuccessHandler``를 실행함
  - 인증에 실패할 경우에는 ``AuthenticationFailureHandler``를 실행



### AuthenticationFilter을 통한 인증 절차

![img](https://blog.kakaocdn.net/dn/ceanmM/btqYIkl9GF0/3AKGUzcpXgrHg1hOFOvNz0/img.png)



1. 클라이언트가 로그인을 위한 Http 요청을 보냄

2. AuthenticationFilter는 아이디와 패스워드 정보를 인터셉트하여 ``AuthenticationManager``를 구현한 ``ProviderManager``에게 인증용 객체를 만들어 위임

   - ``UsernamePasswordAuthenticationToken`` 는 인증용 객체로, ``Authentication``인터페이스를 구현한 것

3. 실제 인증은 ``AuthenticationProvider``가 진행하게 되며, ``ProviderManager``로부터 인증용 객체를 전달받아 인증 처리

4. ``AuthenticationProviders``는  ``UserDetailsService``를 통해 사용자 정보를 읽어들임

   - ``authenticate()`` 메소드를 오버라이딩해서 실행하며, 파라미터로 ``Authentication``이 선언되있음

   - 여기서 ``UserDetailsService``는 인터페이스며, ``User``객체가 이를 구현
   - ``User``은 bean으로 컨테이너에 저장되며, Spring Security가 이를 사용함
   - ``User``객체의 구현을 통해, 개발자가 사용자 정보를 읽어들일 때 DB를 어떻게 활용할 지 결정할 수 있음

5. 인증 성공/실패 여부에 따라 ``AuthenticationSuccessHandler`` 혹은 ``AuthenticationFailureHandler``를 실행



### SecurityConfig 구현

- Spring Security 종속성을 주입받아 기본적인 설정을 추가할 클래스
  - ``@EnableWebSecurity`` 어노테이션을 명시하여 Spring Security  관련 모듈을 활성화
- 오버로딩된 ``configure()`` 메소드들을 통해, Spring Security의 적용범위나 인증 절차 등에 대한 설정을 명시
- 암호화를 할 경우 클래스 내에 ``BCryptPasswordEncoder``를 반환하는 Bean을 정의할 수도 있음



```java
@Configuration
@EnableWebSecurity
public class SecuriyConfig extends WebSecurityConfigurerAdapter{
    
    /*
    	- Spring Security를 무시할 url을 선언
    	- 보통 정적 자원이 저장된 경로를 명시
    */
    @Override
    public void configure(WebSecurity web) throws Exception {
        web,ignoring().antMatchers("/webjars/**"); 
    }
	
    /*
    	- Spring Security 적용 규약 명시
    */
    @Override
    protected void configure(HttpSecurity http) throws Exception {

        http.authorizeRequests()
		    .antMatchers("/**").permitAll() //명시된 경로에 대해서는 모두 접근 가능
			.anyRequest().authenticated();

		http.formLogin()
		   	.loginPage("/loginPage") //명시된 경로를 로그인 페이지로 사용
		   	.loginProcessingUrl("/login") //명시된 경로를 로그인 url로 사용
		   	.usernameParameter("usrId") //파라미터와 동일한 name을 가지는 input 태그로부터 아이디값을 받아옴
			.passwordParameter("usrPw") //파라미터와 동일한 name을 가지는 input 태그로부터 비밀번호값을 받아옴
			.successHandler(successHandler()).failureHandler(failureHandler());

		http.logout()
			.logoutUrl("/logout") //명시된 경로를 로그아웃 url로 사용
			.logoutSuccessUrl("/") //로그아웃 성공시 리디렉션될 url을 지정
			.invalidateHttpSession(true) //인증 관련 세션 정보 삭제
			.deleteCookies("JSESSIONID"); //쿠키 삭제

		http.exceptionHandling()
			.accessDeniedPage("/");  
    }
	
    /*
    	- 사용자 인증 방법 명시(여기서는 인메모리방식을 사용)
    */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            	.passwordEncoder(encoder())
            	.withUser("admin")
            	,password("admin")
            	.authroties("USER","ADMIN");
    }

    @Bean
    public BCryptPasswordEncoder encoder(){

        return new BCryptPasswordEncoder();
    }
    
    /*
    	Sucess, Failure Handler 구현하여 Bean으로 등록
    */
    
    @Bean
    public AuthenticationSuccessHandler successHandler(){
        return new AuthSuccessHandler("/");
    }
    
    @Bean
    public AuthenticationFailureHandler failureHandler(){
        return new AuthFailureHandler();
    }
}
```



- 위의 예시에서 ``configure(AuthenticationManagerBuilder auth)`` 에서 사용자 인증 방법을 명시할 수 있음

  - ``UserDetailsService``의 구현체를 명시하여 인증처리

  - `UserDetailsService` 를  직접 커스터마이징해서 사용하고자 할 경우에는 이를 구현한 객체를 ``auth.userDetailsService()``의 파라미터로 선언함

  - ``inMemoryAuthentication()`` :  인메모리 저장소를 활성화하여, 여기서 사용자 정보를 조회

    ```
    인메모리 데이터 저장소
    
    - 운영을 위한 데이터가 디스크가 아닌 메인 메모리(RAM)에 위치
    - 데이터에 더욱 빠른 접근이 가능하지만, 동시에 메모리의 휘발성으로 인한 문제가 발생할 수 있음
    ```

  - ``jdbcAuthentication()`` :  jdbc 기반의 인증을 진행할 수 있음

    - 이럴 경우에는 DB 연동을 위한 DataSource와 인증, 인가를 위한 쿼리문을 파라미터로 명시

    - 인증을 위한 쿼리문에서는 사용자 아이디(username)와 비밀번호(password)를, 인가를 위한 쿼리문에서는 사용자 아이디와 권한명을 각각 조회

      ```java
      //configure(AuthenticationManagerBuilder auth) 메소드 내부
      auth.jdbcAuthentication()
          	.dataSource(myDataSource) //DB 연동을 위해 사용할 DataSource 객체
          	.rolePrefix("ROLE_") //권한명(authority)에 명시할 접두사
          	.usersByUSernameQuery("Query") //인증을 위한 쿼리
          	.authoritiesByUsernameQuery("Query") //인가를 위한 쿼리
      ```

      





