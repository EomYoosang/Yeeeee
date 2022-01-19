## Spring 환경 설정
### 목차
- 프로젝트 생성
- 라이브러리 살펴보기
- View 환경설정
- H2 데이터베이스 설치
- JPA와 DB 설정, 동작 확인

### 프로젝트 생성
- 스프링 부트 스타터 ([https://start.spring.io/](https://start.spring.io/))
- 사용 기능: web, thymeleaf, jpa, h2, lombok, validation

<img alt="프로젝트 생성" src="generateProject" style="width:80%" />

### lombok 플러그인
lombok을 사용하기 위해 플러그인 추가  
> 최신 버전은 lombok이 기본 템플릿에 추가되어있다.

### View 환경설정
- `Spring/Spring View 생성` 참고

- 스프링 부트 thymeleaf viewName 매핑
   - `resource:templates/` + {ViewName} + `.html`

```java
@Controller
public class HelloController {
	 @GetMapping("hello")
	public String hello(Model model) {
		model.addAttribute("data", "hello!");
		return "hello";
	}
}
```

**thymeleaf 템플릿엔진 동작 확인**  
```html:hello.html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
 <title>Hello</title>
 <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<p th:text="'안녕하세요. ' + ${data}" >안녕하세요. 손님</p>
</body>
</html>
```

### H2 데이터베이스 설정
- h2 데이터베이스는 개발이나 테스트 용도로 가볍고 편리한 DB
- 웹 화면 콘솔을 제공한다
- 이 글에선 1.4.200버전 사용
- 데이터베이스 파일 생성은 `Spring/Spring DB 접근 기술` 참고


**application.yml**
```yml
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/jpashop
    username: sa
    password:
    driver-class-name: org.h2.Driver

  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
#       show_sql: true
        format_sql: true

logging:
  level:
    org.hibernate.SQL: debug
```
- spring.jpa.hibernate.ddl-auto: create
   - 이 옵션을 추가하면 애플리케이션 실행 시점에 테이블을 drop하고, 다시 생성한다.
> 참고: 모든 로그 출력은 가급적 로거를 통해 남겨야 한다.  
`show_sql` 옵션은 `System.out`에 하이버네이트 실행 SQL을 남긴다.  
`org.hibernate.SQL`: 옵션은 logger를 통해 하이버네이트 실행 SQL을 남긴다.
> 주의: yml파일은 띄어쓰기 2칸으로 계층을 나눔

**동작 확인하기**  

**회원 엔티티**  
```java:Member.java
@Entity
@Getter
@Setter
public class Member {

    @Id
    @GeneratedValue
    private Long id;
    private String username;
}

```
**회원 리포지토리**  
```java:MemberRepository.java
@Repository
public class MemberRepository {
    @PersistenceContext
    private EntityManager em;

    public Long save(Member member) {
        em.persist(member);
        return member.getId();
    }

    public Member find(Long id) {
        return em.find(Member.class, id);
    }
}

```

**회원 리포지토리 테스트**  
```java:MemberRepositoryTest.java
@RunWith(SpringRunner.class)
@SpringBootTest
public class MemberRepositoryTest {
    @Autowired MemberRepository memberRepository;

    @Test
    @Transactional
    @Rollback(false)
    public void testMember() {
        Member member = new Member();
        member.setUsername("memberA");
        Long savedId = memberRepository.save(member);
        Member findMember = memberRepository.find(savedId);
        Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());

        Assertions.assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
        Assertions.assertThat(findMember).isEqualTo(member); //JPA 엔티티 동일성 보장
    }
}
```
> 이 글에서는 JUnit4 사용  

> 중요!  
h2데이터베이스를 실행중인지, 경로가 맞는지 확인
> 주의! Execution failed for task ':test'. 에러  
Preference > Build, Execution, Deployment > Build Tools > Gradle 에서 Run test using을 gradle에서 intelliJ IDEA로 변경

### 쿼리 파라미터 로그 남기기
- 로그에 `org.hibernate.type` 추가 : SQL 실행 파라미터를 로그로 남김
- 외부 라이브러리 사용
   - [https://github.com/gavlyukovskiy/spring-boot-data-source-decorator](https://github.com/gavlyukovskiy/spring-boot-data-source-decorator)  
> 개발 단계에는 괜찮지만 배포할 땐 성능테스트 필수!  

