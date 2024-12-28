# Spring 5 MVC Application Using Java Configuration with Spring Security

This tutorial explains how to create a **Spring 5 MVC Application** using pure Java configuration, integrated with **Spring Security** for user authentication using in-memory user details.

---

## Prerequisites

- Java 8 or higher
- Maven or Gradle
- Spring Framework 5.x
- Spring Security 5.x
- A development environment (e.g., IntelliJ IDEA, Eclipse, VS Code)
- Apache Tomcat or any web server

---

## Project Setup

### 1. Add Maven Dependencies

Update your `pom.xml` file to include the following dependencies:

```xml
<dependencies>
    <!-- Spring Core -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.30</version>
    </dependency>
    
    <!-- Spring Web -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.30</version>
    </dependency>

    <!-- Spring Security -->
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-config</artifactId>
        <version>5.8.7</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-web</artifactId>
        <version>5.8.7</version>
    </dependency>

    <!-- JSP support -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>4.0.1</version>
        <scope>provided</scope>
    </dependency>

    <!-- JSTL -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
</dependencies>




2. Update Java Configuration
AppConfig.java
This configuration remains the same as before for Spring MVC:

java
Copy code
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.web.servlet.view.InternalResourceViewResolver;

@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.example")
public class AppConfig implements WebMvcConfigurer {

    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/resources/**").addResourceLocations("/resources/");
    }
}
SecurityConfig.java
Add a Spring Security configuration class for authentication:

java
Copy code
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/resources/**", "/login").permitAll() // Allow access to resources and login page
            .anyRequest().authenticated() // All other URLs require authentication
            .and()
            .formLogin()
            .loginPage("/login") // Custom login page URL
            .defaultSuccessUrl("/") // Redirect after successful login
            .permitAll()
            .and()
            .logout()
            .permitAll();
        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .withUser("admin").password(passwordEncoder().encode("admin123")).roles("ADMIN")
            .and()
            .withUser("user").password(passwordEncoder().encode("user123")).roles("USER");
    }
}
AppInitializer.java
Update your initializer to include SecurityConfig:

java
Copy code
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class AppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SecurityConfig.class}; // Add SecurityConfig
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{AppConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
3. Update Controller
HomeController.java
java
Copy code
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {

    @GetMapping("/")
    public String home(Model model) {
        model.addAttribute("message", "Welcome to the secured Spring MVC Application!");
        return "home"; // Corresponds to home.jsp
    }

    @GetMapping("/login")
    public String login() {
        return "login"; // Corresponds to login.jsp
    }
}
4. Views
home.jsp
jsp
Copy code
<!DOCTYPE html>
<html>
<head>
    <title>Home</title>
</head>
<body>
    <h1>${message}</h1>
    <a href="/logout">Logout</a>
</body>
</html>
login.jsp
jsp
Copy code
<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
</head>
<body>
    <h1>Login</h1>
    <form action="/login" method="post">
        <label for="username">Username:</label>
        <input type="text" id="username" name="username" required><br><br>
        <label for="password">Password:</label>
        <input type="password" id="password" name="password" required><br><br>
        <button type="submit">Login</button>
    </form>
</body>
</html>
Running the Application
Deploy the application on Apache Tomcat or any servlet container.
Access the application in your browser:
Navigate to http://localhost:8080/login to see the custom login page.
Use the following credentials:
Username: admin | Password: admin123
Username: user | Password: user123
After login, you'll be redirected to the home page.
Enhancements
Add role-based access control (RBAC).
Store user details in a database instead of in-memory.
Implement OAuth2 authentication or JWT for RESTful APIs.
Author
Created by [Your Name].

License
This project is licensed under the MIT License.

vbnet
Copy code

This updated `README.md` includes the Spring Security integration details, making it comprehensive fo
