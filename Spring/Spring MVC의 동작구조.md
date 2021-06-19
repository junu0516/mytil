### MVC2 패턴

- 어플리케이션을 구성하는 요소를 역할에 따라 세가지 모듈로 구분
- __Model__ : View의 렌더링에 필요한 데이터나 비즈니스 관련 로직을 처리
- __View__ : Model의 로직을 반영하여 사용자에게 보여질 화면을 담당
- __Controller__ : View와 Model 사이에서 사용자의 입력 혹은 요청에 관한 비즈니스 로직을 처리



- ![img](https://blog.kakaocdn.net/dn/bHkSm5/btqUL8paQiW/n4ZXeW70kbO6mHCeIQ3ZkK/img.png)

- 프론트 컨트롤러는 우선적으로 클라이언트로부터의 요청을 받아, 개별 컨트롤러에 위임
- 개별 컨트롤러는 핸들러(Handler) 라고도 하며, Spring에서는 보통 DI 컨테이너에 보관된 Bean을 통해 비즈니스 로직의 처리 결과를 프론트 컨트롤러로 전달함
- 프론트 컨트롤러는 다시 이를 반영하여 생성된 View 받아 클라이언트에게 응답



### Spring MVC의 동작구조

![img](https://blog.kakaocdn.net/dn/rziP4/btqUGUL2tR6/4JESRmCdjSN2DQBL5xlaOk/img.png)

* __DispatcherServlet__ : 사용자의 모든 요청을 받아 처리(Front Controller에 해당)
* __HandlerAdapter__ 이 매핑된 컨트롤러의 실행을 요청함
* __Controller__은 매핑 후 직접 요청을 처리하고, 처리 결과를 반환
  * 반환된 결과가 반영된 ``ModelAndView`` 객체가 생성되며 여기에는 View Name을 비롯한 랜더링될 View에 관한 정보 및 데이터가 포함되어 있음
  * 보통 입력을 처리한 후, 구체적인 비즈니스 로직은 ``Service Layer``에서 수행하게 되며, 데이터베이스와 실제로 데이터를 주고받을 필요가 있을 경우에는 ``Repository Layer``혹은 ``Data Access Layer``에 접근함
* __View Resolver__ : View Name을 확인 후 실제 컨트롤러로 부터 받은 로직 처리결과를 반영할 View 파일을 탐색 후 최종 ``View``를 생성
  * View에는 ``jsp``, ``mustache``,  ``html``, 등의 다양한 형식이 존재할 수 있음



### Dispatcher Servlet

- 여러 ``Request`` 들을 개별 ``Controller``에 위임
- DispatcherServlet이 Front Controller로 기능하도록, ``web.xml``에  ``java config`` 클래스를 명시

```java
package com.junu.webmvc.config;
 
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.DefaultServletHandlerConfigurer;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
import org.springframework.web.servlet.view.InternalResourceViewResolver;
 
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = {"com.junu.webmvc.controller"}) 
public class WebMvcContextConfiguration extends WebMvcConfigurerAdapter {
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/assets/**").addResourceLocations("classpath:/META-INF/resources/webjars/").setCachePeriod(31556926);
        registry.addResourceHandler("/css/**").addResourceLocations("/css/").setCachePeriod(31556926);
        registry.addResourceHandler("/img/**").addResourceLocations("/img/").setCachePeriod(31556926);
        registry.addResourceHandler("/js/**").addResourceLocations("/js/").setCachePeriod(31556926);
    }
    
    @Override //default servlet handler 
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable(); 
        //매핑정보가 없는 url 요청에 대해서는 default servlet이 처리하도록 하는 것
    }
 
    @Override 
    public void addViewControllers(ViewControllerRegistry registry) {
        System.out.println("addViewControllers 호출");
        registry.addViewController("/").setViewName("main"); 
        // "/"라는 매핑값으로 들어온 요청에 대해서는 'main'이라는 view name을 찾아 사용하라는 것
    }
    
    @Bean
    public InternalResourceViewResolver getInternalResourceViewResolver() {
        InternalResourceViewResolver resolver = new 	InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/"); 
        resolver.setSuffix(".jsp");
        //addViewControllers 메소드를 통해 단순히 view name만 받았기 때문에 여기서 view 파일의 경로와 확장자를 추가시켜 주는 것
        return resolver;
    }
 
}

```

- 여기서 ``WebMvcConfigurerAdapter`` 클래스를 상속받아 Config 클래스를 생성하였으며, ``@EnableWebMvc`` 어노테이션의 명시를 통해 Spring MVC의 사용을 위한 기본적인 설정을 자동으로 실행시켰음
  - ``getInternalResourceViewResolver``은 컨트롤러가 View Name을 반환할 경우, 이를 반영하여 온전한 View 파일의 탐색을 위한 기본적인 정보를 담고 있음(경로 및 파일 확장자)
  - ``addResourceHandlers`` 는 특정 경로의 요청에 대해, 자원에 대한 접근임을 명시하여 매핑될 View 파일을 찾지 못하는 오류의 발생을 방지함

- 이후 ``web.xml``에 아래와 같이 Front Controller로 기능할 DispatcherServlet을 명시

```xml
<?xml version="1.0" encoding="UTF-8"?>

<web-app>
  <display-name>Archetype Created Web Application</display-name>
  
  <!-- WebMvcConfiguration.java 서블릿을 FrontController로 설정하기 -->
  <!-- 아래 설정을 입력해야 스프링에서 제공하는 DispatcherServlet이 FrontController로 기능하게 할 수 있음 -->
  <servlet>
  	<servlet-name>mvc</servlet-name> <!-- 매핑시킬 서블릿 이름 -->
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class> <!-- 서블릿의 클래스 정보 (어떤 클래스인지)-->
  	<init-param> <!-- contextClass 등룍 -->
      <param-name>contextClass</param-name>
      <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value> <!-- 쉽게 말해 왼쪽에 있는 클래스 import 하겠다는 것 ... -->
    </init-param>
  	<init-param><!-- 설정 정보 읽어들이기 위한 클래스 등록-->
  		<param-name>contextConfigLocation</param-name>
  		<param-value>com.junu.webmvc.config.WebMvcContextConfiguration</param-value> <!-- 해당 경로에 있는 클래스에 작성한 정보를 읽어들임(import 하겠다는 것) -->
  	</init-param>
  	<load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
  	<servlet-name>mvc</servlet-name> <!-- 모든 요청에 대해 mvc라는 이름의 서블릿에서 요청을 받도록 함 -->
  	<url-pattern>/</url-pattern> <!-- /로 할 경우 모든 요청을 받음 -->
  </servlet-mapping>
</web-app>
```



