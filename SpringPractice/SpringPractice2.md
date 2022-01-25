## 도메인 분석 설계
### 요구사항 분석
<img alt="ui" src="ui.png" />   

**실제 동작하는 화면**

**기능 목록**
- 회원 기능
   - 회원 등록
   - 회원 조회
- 상품 기능
   - 상품 등록
   - 상품 수정
   - 상품 조회
- 주문 기능
   - 상품 주문
   - 주문 내역 조회
   - 주문 취소
- 기타 요구사항
   - 상품은 재고 관리가 필요
   - 상품의 종류는 도서, 음반, 영화
   - 상품을 카테고리로 구분
   - 상품 주문시 배송 정보를 입력

### 도메인 모델과 테이블 설계
<img alt="model" src="model.png" />  

- 회원vs주문vs상품: 회원이 여러 상품을 주문할 수 있고, 한 번에 여러 상품을 선택할 수 있으므로 다대다 관계이다. 하지만 다대다 관계는 관계형 데이터베이스나 엔티티에서 거의 사용하지 않으므로 위처럼 주문상품이라는 엔티티를 추가해서 일대다, 다대일 관계로 풀어냈다.
- 상품 분류: 상품은 도서, 음반, 영화로 구분되는데 상품이라는 공통 속성을 사용하므로 상속 구조를 표현했다.

#### 회원 엔티티 분석
<img alt="memberEntity" src="memberEntity.png" />  

- 회원(Member): 이름과 임베디드 타입인 주소(Address), 주문(orders) 리스트를 가진다.
- 주문(Order): 한 번 주문시 여러 상품을 주문할 수 있으므로 주문과 주문상품(OrderItem)은 일대다 관계다.  
주문은 상품을 주문한 회원과 배송정보, 주문 날짜, 주문 상태(status)를 가지고 있다. 주문 상태는 열거형을 이용했는데 주문(Order), 취소(CANCEL)을 표현할 수 있다.
- 주문상품(OrderItem):  주문한 상품 정보와 주문 금액(orderPrice), 주문 수량(count) 정보를 가지고 있다.  
보통 OrderLine, LineItem으로 많이 표현한다.
- 배송(Delivery): 주문시 하나의 배송 정보를 생성한다. 주문과 배송은 일대일 관계다.
- 카테고리 상품과 다대다 관계를 맺는다. parent, child로 부모, 자식 카테고리를 연결한다.
- 주소(address): 값 타입(임베디드 타입)이다. 회원과 배송에서 사용한다.

> 참고: 회원이 주문을 하기 때문에, 회원이 주문리스트를 가지는 것은 얼핏 보면 잘 설계한 것 같지만, 객체 세상은 실제 세계와 다르다. 실무에서는 회원이 주문을 참조하지 않고, 주문이 회원을 참조하는 것으로 충분하다.

> 양방향 보단 단방향, 다대다보단 일대다 관계가 권장된다  

#### 회원 테이블 분석

<img alt="memberTable" src="memberTable.png" />  

- MEMBER: 회원 엔티티의 Address 임베디드 타입 정보가 회원 테이블에 그대로 들어갔다. DELIVERY 테이블도 마찬가지
- ITEM: 앨범, 도서, 영화 타입을 통합해서 하나의 테이블로 만들었다. DTYPE 컬럼으로 타입을 구분한다. 싱글테이블 전략 사용.

> 싱글테이블 전략: 구분되는 컬럼을 한 테이블에 다 넣고 DTYPE으로 구분

> 참고: 테이블명이 ORDERS인 것은 ORDER가 예약어인 경우가 많기 때문

> 참고: 실제 코드에서는 소문자와 언더스코어(_)를 사용

#### 연관관계 매핑 분석
- 회원과 주문: 일대다, 다대일의 양방향 관계. 연관관계의 주인을 정해야 하는데, 외래 키가 있는 주문을 연관관계의 주인으로 정하는 것이 좋다. 그러므로 Order.member를 ORDERS.MEMBER_ID 외래 키와 매핑한다.

- 주문상품과 주문: 다대일 양방향 관계. 외래 키가 주문상품에 있으므로 주문상품이 연관관계의 주인이다. 그러므로 OrderItem.order를 ORDER_ITEM.ORDER_ID 외래키와 매핑한다.

- 주문상품과 상품: 다대일 단방향 관계. OrderItem.itemdmf ORDER_ITEM.ITEM_ID 외래 키와 매핑한다.

- 주문과 배송: 일대일 양방향 관계. Order.delivery를 ORDERS.DELIVERY_ID 외래 키와 매핑한다. 연관관계의 주인, 즉, fk를 갖는 테이블은 어느쪽이든 상관 없지만 액세스가 많은 주문에 놓는다.

- 카테고리와 상품: @ManyToMany를 사용해서 매핑한다. (실무에서 @ManyToMany 사용 X. 다대다 관계 예제를 위해 사용)

> **참고: 외래 키가 있는 곳을 연관관계의 주인으로.**  
연관관계의 주인은 비즈니스 상의 우위가 아닌 외래 키를 누가 관리하는지에 따라 결정. 
  

### 엔티티 클래스 개발
- 예제에서는 설명을 쉽게하기 위해 엔티티 클래스에 Getter, Setter를 모두 열고, 단순하게 설계
- 실무에서는 가급적 Setter는 꼭 필요한 경우에만 사용

> 참고: 이론적으로 Getter, Setter 모두 제공하지 않고, 꼭 필요한 별도의 메서드를 제공하는 것이 이상적. 실무에서는 엔티티의 데이터를 조회할 일이 많으므로 Getter는 모두 열어두는 것이 편리하다. 

**회원 엔티티**  
```java
@Entity
@Getter
@Setter
public class Member {

    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;

    private String username;

    @Embedded
    private Address address;

    @OneToMany(name = "member")
    private List<Order> orders = new ArrayList<>();
}

```
> 참고: 엔티티의 식별자는 `id`를 사용하고 PK 컬럼 명은 `member_id`를 사용. 엔티티는 타입이 있으므로 `id`필드 만으로 쉽게 구불할 수 있고, 테이블은 타입이 없어 쉽게 구분할 수 없으므로 테이블 명으로 id를 명시. `테이블 명`+`_id`또는 `테이블명`+`Id`를 사용. 둘 중 어느 것을 사용해도 상관 없으나 일관성이 중요!  

**주문 엔티티**  
```java
@Entity
@Table(name = "orders")
@Getter @Setter
public class Order {

    @Id @GeneratedValue
    @Column(name = "order_id")
    private Long id;

    @ManyToOne
    @JoinColumn(name = "member_id")
    private Member member;

    @OneToMany(mappedBy = "order")
    private List<OrderItem> orderItems = new ArrayList<>();

    @OneToOne
    @JoinColumn(name = "delivery_id")
    private Delivery delivery;

    private LocalDateTime orderDate;

    @Enumerated(EnumType.STRING)
    private OrderStatus orderStatus;    // 주문상 [Order, Cancel]
}
```
위의 주문과 회원의 연관관계의 주인은 fk를 갖고있는 주문 쪽.
>일대다 양방향 관계의 연관관계의 주인인 엔티티 클래스에는 `@JoinColumn` annotation을 이용해 어느 컬럼의 값을 fk로 사용할 것인지 명시, 주인이 아닌 엔티티 클래스에서는 주인인 엔티티 클래스의 어떤 변수에 명시되는지 표시. (Member클래스의 `@OneToMany(name = "member")` Annotation)

**주문상품 엔티티**  
```java
@Entity
@Getter @Setter
public class OrderItem {

    @Id @GeneratedValue
    @Column(name = "order_item_id")
    private Long id;

    @ManyToOne
    @JoinColumn(name = "item_id")
    private Item item;

    @ManyToOne
    @JoinColumn(name = "order_id")
    private Order order;

    private int orderPrice;
    private int count;
}

```

**상품 엔티티**  
```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "dtype")
@Getter @Setter
public abstract class Item {

    @Id @GeneratedValue
    @Column(name = "item_id")
    private Long id;

    private String name;
    private int price;
    private int stockQuantity;

    @ManyToMany(mappedBy = "items")
    private List<Category> categories = new ArrayList<>();

}
```
>상품은 싱글테이블 전략을 사용하여 세부 상품을 상속시킨다. `@Inheritence(strategy = InheritanceType.SINGLE_TABLE` annotation사용. 자식클래스는 [깃허브](https://github.com/EomYoosang/SpringPractice/tree/main/src/main/java/jpabook/jpashop/domain/item)에 기록  

**배송 엔티티**
```java
package jpabook.jpashop.domain;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;

@Entity
@Getter @Setter
public class Delivery {

    @Id @GeneratedValue
    @Column(name = "delivery_id")
    private Long id;

    @OneToOne(mappedBy = "delivery")
    private Order order;

    @Embedded
    private Address address;

    @Enumerated(EnumType.STRING)
    private DeliveryStatus status;  // READY, COMP
}
```
**카테고리 엔티티**
```java
@Entity
@Getter @Setter
public class Category {

    @Id @GeneratedValue
    @Column(name = "category_id")
    private Long id;

    private String name;

    @ManyToMany
    @JoinTable(name = "category_item",
            joinColumns = @JoinColumn(name = "category_id"),
            inverseJoinColumns = @JoinColumn(name = "item_id"))
    private List<Item> items = new ArrayList<>();

    @ManyToOne
    @JoinColumn(name = "parent_id")
    private Category parent;

    @OneToMany(mappedBy = "parent")
    private List<Category> child = new ArrayList<>();
}
```
>참고: 실무에서는 `@ManyToMany`를 사용하지 말자. 편리해보이지만 중간 테이블(카테고리에선 CATEGORY_ITEM)에 컬럼을 추가할 수 없고, 세밀하게 쿼리를 실행시키기 어렵기 때문에 한계가 있다. 실무에서는 중간 엔티티(`CategoryItem`을 만들고 `@ManyToOne`, `@OneToMany`로 매핑해 다대다 관계를 일대다, 다대일 매핑으로 풀어낸다.

**주소 값 타입**
```java
@Embeddable
@Getter
public class Address {

    private String city;
    private String street;
    private String zipcode;
}
```
> 참고: `@Embeddable`이나 `!Embedded` 둘 중 하나만 써도 되지만 이 글에선 둘 다 사용하였다.  

> 참고: JPA스펙 상 엔티티나 임베디트타입은 `public` 또는 `protected`로 설정하며 `protected`가 더 안전하다. JPA가 이런 제약을 두는 이유는 JPA 구현 라이브러리가 객체를 생성할 때 리플랙션 같은 기술을 사용할 수 있도록 지원해야 하기 때문이다.

### 엔티티 설계시 주의점
#### 엔티티에는 가급적 Setter를 사용하지 말자
Setter가 모두 열려있으면 변경 포인트가 너무 많아서, 유지보수가 어렵다.
#### 모든 연관관계는 지연로딩으로 설정
- 즉시로딩(EAGER)은 예측이 어렵고, 어떤 SQL이 실행될지 추적하기 어렵다. 특히 JPQL을 실행할 때 N+1문제가 자주 발생한다.
- 실무에서 모든 연관관계는 지연로딩(LAZY)로 설정한다.
- 연관된 엔티티를 함께 DB에서 조회해야 하면, fetch join 또는 엔티티 그래프 기능을 사용한다.
- `@XToOne`관계는 기본이 즉시 로딩이므로 직접 지연로딩으로 설정해야 한다.

> **기존 코드의 `@XToOne`을 전부 지연로딩으로 변경한다.**


#### 컬렉션은 필드에서 초기화하자.
- null문제에서 안전하다.
- 하이버네이트는 엔티티를 영속화 할 때, 컬렉션을 감싸서 하이버네이트가 제공하는 내장 컬렉션으로 변경한다. 만약 getOrders()처럼 임의의 메서드에서 컬렉션을 잘못 생성하면 하이버네이트 내부 메커니즘에 문제가 발생할 수 있다. 따라서 필드레벨에서 생성하는 것이 가장 안전하고, 코드도 간결하다. 아래 예시 코드를 통해 알 수 있다.  
```java
Member member = new Member();
System.out.println(member.getOrders().getClass());
em.persist(team);
System.out.println(member.getOrders().getClass());

// 출력 결과
class java.util.ArrayList
class org.hibernate.collection.internal.PersistentBag
```

#### 테이블, 컬럼명 생성 전략
스프링부트에서 하이버네이트 기본 매핑 전략을 변경해서 실제 테이블 필드 명은 다름
하이버네이트 기존 구현: 엔티티의 필드명을 그대로 테이블 명으로 사용  
(`SpringPhysicalNamingStrategy`)

시프링 부트 신규 설정(엔티티(필드) -> 테이블(컬럼))  
1. 카멜 케이스 -> 언더스코어(memberPoint -> member_point)
2. 점 -> _(언더스코어)
3. 대문자 -> 소문자

**적용 2단계**  
1. 논리명 생성: 명시적으로 컬럼, 테이블 명을 직접 적지 않으면 ImplicitNamingStrategy 사용  
`spring.jpa.hibernate.naming.implicit-strategy`: 테이블이나, 컬럼명을 명시하지 않을 때 논리명 적용.
2. 물리명 적용:  
`spring.jpa.hibernate.naming.physical-strategy`: 모든 논리명에 적용됨. 실제 테이블에 적용 (username -> usernm 등으로 회사 룰로 바꿀 수 있음)

> 논리명, 물리명은 추가로 작성 예정

**스프링 부트 기본 설정**  
`spring.jpa.hibernate.naming.implicit-strategy:  
org.springframework.boot.orm.jpa.hibernate.SpringImplicitNamingstrategy`  
`spring.jpa.hibernate.naming.physical-strategy:  
org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy`

#### Cascade
엔티티의 상태는 크게 아래 4가지의 상태로 나뉜다.  
1. `Transient`: 객체를 생성하고, 값을 주어도 JPA나 hibernate가 그 객체에 관해 아무것도 모르는 상태.
2. `Persistent`: 저장을 하고나서, JPA가 아는 상태(관리하는 상태)가 된다. 그러나 .save()를 했다고 해서, 이 순간 바로 DB에 객체에 대한 데이터가 들어가는 것은 아니다. JPA가 persistent 상태로 관리하고 있다가, 후에 데이터를 저장한다.
3. `Detached`: JPA가 더이상 관리하지 않는 상태. JPA가 제공해주는 기능들을 사용하고 싶다면, 다시 persistent상태로 돌아가야 한다.
4. `Removed`: JPA가 관리하는 상태이긴 하지만, 실제 commit이 일어날 때, 삭제가 일어난다.

**cascade는 이러한 상태를 전이시키는 것이다.**

Order 엔티티를 예시로 Delivery 객체에 `@OneToOne(fetch = LAZY, cascade = CascadeType.ALL)` annotation을 넣으면, Order가 저장될 때 Delivery도 함께 저장된다.

#### 연관관계 관계 메서드
양방향 연관관계를 맺을 때에는, 양쪽 모두 관계를 맺어주어야한다.  
사실 JPA의 입장에서 보았을 때에는 외래키 관리자(연관관계의 주인) 쪽에만 관계를 맺어준다면 정상적으로 양 쪽 모두에서 조회가 가능하다. 하지만 객체까지 고려한다면, 양쪽 다 관계를 맺어야한다.  
즉, 객체의 양방향 연관관계는 양쪽 모두 관계를 맺어주어야 순수한 객체 상태에서도 정상적으로 동작한다. 객체 양방향에서 모두 관계를 맺어주기 위한 메서드를 연관관계 편의 메서드라 한다.
```java
@Entity
@Table(name = "orders")
@Getter @Setter
public class Order {

	...

    // 연관관계 편의 메서드
    public void setMember(Member member) {
        this.member = member;
        member.getOrders().add(this);
    }

    public void addOrderItem(OrderItem orderItem) {
        orderItems.add(orderItem);
        orderItem.setOrder(this);
    }

    public void setDelivery(Delivery delivery) {
        this.delivery = delivery;
        delivery.setOrder(this);
    }
}
```