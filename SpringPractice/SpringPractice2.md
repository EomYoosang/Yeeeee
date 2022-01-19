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

- 주문과 배송: 일대일 양방향 관계. Order.delivery를 ORDERS.DELIVERY_ID 외래 키와 매핑한다.

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

    @OneToMany
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
}
```
위의 주문과 회원의 연관관계의 주인은 fk를 갖고있는 주문 쪽.