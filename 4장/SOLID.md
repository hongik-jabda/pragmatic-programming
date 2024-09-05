# SOLID 원칙
단일 책임 원칙(SRP)
개방 페쇄 원칙(OCP)
리스코프 치환 원칙(LSP)
인터페이스 분리 원칙(ISP)
의존성 역전 원칙(DIP)
- 영향 범위: 코드 변경으로 인한 영향 범위가 어떻게 되는가?
 
- 의존성: 소프트웨어의 의존성 관리가 제대로 이루어지고 있는가?

- 확장성: 쉽게 확장 가능한가?

## 단일 책임 원칙(SRP)
클래스에 너무 많은 책임이 할당돼서는 안되며, 단 하나의 책임만 있어야 한다.
- 그래야 변경이 쉬워진다.

- ex) 8000라인이나 되는 클래스?
- 모두 해당 클래스를 참조
- 영향 범위 알 수 없으니 코드 '변경' 어려워짐.
- 따라서 특정 역할 달성에만 집중할 수 있게 해야함.
- 다시 말해, 변경으로 인한 영향 범위를 최소화(항상성 유지)해야.

### 책임?
```java
class Developer {
	public String createFrontendCode() {
		// 프론트엔드 코드를 만듭니다.
	}
	public String publishFrontend() {
		// 프론트엔드 서비스를 배포합니다.
	}
	public String createBackendCode() {
		// 백엔드 코드를 만듭니다.
	}
	public String serveBackend() {
		// 백엔드 서비스를 배포합니다.
	}
}
```
- 단일 책임 원칙 위배? 
- 개발자가 프론트엔드 개발자의 책임과 백엔드 개발자의 책임 모두를 가짐.
#### 책임을 프론트엔드 개발자와 백엔드 개발자로 분류
1. 프론트엔드: createFrontendCode, publishFrontend
2. 백엔드: createBackendCode, serveBackend
#### 책임을 프론트엔드 개발자, 백엔드 개발자, 시스템 운영자로 분류
1. 프론트엔드 개발자: createFrontendCode
2. 백엔드 개발자: createBackendCode
3. 시스템 운영자: publishFrontend, serveBackend
#### 책임을 시스템 개발자로 분류
1. 시스템 개발자: createFrontendCode, publishFrontend, createBackendCode, serveBackend

- 각 의견 모두 각 관점에서 맞말.
- 따라서 책임이란 무엇이고 이를 어떻게 나눌지 기준이 필요하다. 

### 액터(actor)
로버트 C. 마틴 왈, 하나의 모듈은 오직 하나의 액터에 대해서만 책임져야 한다.
- 액터란, 메시지를 전달하는 주체.
- 시스템에서 어떤 모듈이나 클래스를 사용하게 될 액터가 몇 명인지를 먼저 확인해야 한다.
- 어떤 클래스를 사용할 액터가 한 명이라면 SRP 지키고 있는 것, 여럿이라면 위반하고 있는 것.
- 위의 예에서, Developer 객체를 사용하는 액터를 백엔드 개발을 시키는 액터와 프론트엔드 개발을 시키는 액터로 분리한다면 Developer는 책임 두 가지
- 풀스택 개발을 시키는 액터로 하나만 있으면 Developer는 책임 한 가지
- 따라서, 단일 책임 원칙을 이해하려면 시스템에 존재하는 액터를 먼저 이해해야.
- 클래스를 변경할 이유는, 유일하게 액터의 요구사항이 변경될 때로 제한되어야 함.
- 반례로 액터가 여럿이라면 클래스를 변경해야 할 이유도 여럿이 됨.
> 태순밥먹어Service와 태순공부해Service가 있다고 하면, 이 둘은 서로 다른 액터에게서 지시를 받아야 하는데
**태순Service로 통합해버리면 두 가지의 역할을 가지게 되므로 원칙 위반.**

## 개방 폐쇄 원칙(OCP)
확장에는 열려 있고 변경에는 닫혀 있어야 한다.
- 기존 코드를 수정하지 않고 확장이 가능해야 한다.
- 구현에 의존하기보단 역할에 의존하는 코드를 만들어라.
## 리스코프 치환 법칙(LSP)
파생 클래스는 기본 클래스를 완벽히 대체할 수 있어야 한다.
```Java
@Getter
@Setter
@AllArgsConstructor
class Rectangle {
	protected long width;
	protected long height;

	public long calculateArea() {
		return width * height;
	}
}
class Square extends Rectangle {
	public Square(long length) {
		super(length, length);
	}
}
```
```Java
Rectangle rectangle = new Square(10);
rectangle.setHeight(5);
System.out.printn(rectangle.calculateArea());  // 50
```
- Square은 Rectangle의 동작을 완전히 대체하지 못함.

```java
class Square extends Rectangle {
	public Square(long length) {
		super(length, length);
	}

	@Override
	public void setHeight(long height) {
		super.width = height;
		super.height = height;
	}
}
```
- 이와 같이 기본 클래스에 의도에 따라 오버라이딩을 했지만, 그래도 대체할 수 있다고는 할 수 없다.
```Java
public void myFunction(Rectangle rectangle) { // 매개변수로 Square 객체
	rectangle.setHeight(100);
	System.out.println(rectangle.getWidth()); // 100

	rectangle.setHeight(50);
	System.out.println(rectangle.getWidth());
}
```
- 높이를 변경했는데 너비까지 변경됨.
- 맥락을 모르면, 매개변수로 Square이 들어갔다는 것을 눈치채지 못할 것.
- 이 예제에서 Square가 온전히 리스코프 치환 법칙을 지키도록 바꾸는 것은 어렵다.
- 따라서, 코드만 보고 기본 클래스의 의도 파악하기는 힘듦.
- 초기 개발자에게 물어봐야 할 수도. or 초기 개발자가 모든 의도를 테스트 코드로 만들어 두면 불편 감소.

## 인터페이스 분리 원칙(ISP)
어떤 클래스가 자신에게 필요하지 않은 인터페이스의 메서드를 구현하거나 의존하지 않아야 함.
- 단일 책임 원칙과 밀접한 관련. 
```Java
public class LifecycleBean implements // 스프링 프레임워크의 LifecycleBean 클래스의 코드 일부.
	BeanNameAware,
	BeanFactoryAware,
	InitializingBean,
	DisposableBean {
	// ...
}
```
- BeanNameAware과 BenaFactoryAware을 분리하는 등 인터페이스 세분화. 왜 BeanAware로 통합하지 않고?

```Java
public aspect AnnotationBeanConfigurerAspect     // 스프링 프레임워크에 있는 코드 일부
	extend Abstr...
	implements BeanFactoryAware, InitializingBean, DisposableBean {
	//...
}
```
- BeanFactoryAware 인터페이스만 구현. 실제 사용할 게 그것뿐이라!

- BeanAware은 Bean과 관련된 모든 활동을 구독하겠다는 의미가 되는데, 인터페이스의 정의가 지나치게 넓어 역할이 모호해지면서 응집도는 낮아짐.
- 따라서 인터페이스, 즉 역할을 세분화하는 것은 기능적 응집도를 높이는 길

### 예외?
JPA의 Repository는 그 자체로 너무 많은 역할을 가지고 있지만, 세분화하지 않음.
- 개발 충분히 잘해오고 있다.
- 무조건 세분화하는 것은 좋지 않고, 밸런스 잘 유지해야

## 의존성 역전 원칙(DIP)
- 상위 모듈은 하위 모듈에 의존해서는 안된다. 상위 모듈과 하위 모듈 모두 추상화에 의존해야 한다.
- 추상화는 세부 사항에 의존해서는 안된다. 세부 사항이 추상화에 의존해야 한다.

### 의존성?
의존 : 다른 객체나 함수를 사용하는 상태
```Java
class Car implements Vehicle {
}
```
- 의존성과 결합도를 낮추기 위한 기법 -- 의존성 주입.

### 의존성 주입(Dependency Injection, DI)
필요한 의존성을 외부에서 넣어주는 것
```Java
class HamburgerChef {
	public Food make() {
		Bread bread = new WheatBread();
		Meat meat = new Beef();
		Vegetable vegetable = new Lettuce();
		Sauce sauce = new TomatoSauce();
		return Hamburger.builder()
			.bread(bread)
			.meat(meat)
			.vegetable(vegetable)
			.sauce(sauce)
			.build();
	}
}
```
- 이를 외부에서 재료를 전달받도록 고치면
```Java
class HamburgerChef {
	public Food make(
		Bread bread,
		Meat meat,
		Vegetable vegetable,
		Sauce sauce) {
		return Hamburger.builder()
			.bread(bread)
			.meat(meat)
			.vegetable(vegetable)
			.sauce(sauce)
			.build();
	}
}
```
- 이게 바로 메서도의 매개변수로 의존성을 주입받는 매개변수 주입
```Java
class HamburgerChef {
	private Bread bread;
	private Meat meat;
	private Vegetable vegetable;
	private Sauce sauce;
	
	public HamburgerChef(
		Bread bread,
		Meat meat,
		Vegetable vegetable,
		Sauce sauce) {
		this.bread = bread;
		this.meat = meat;
		this.vegetable = vegetable;
		this.sauce = sauce;
	}
}
	public Food make() {
		return Hamburger.builder()
			.bread(bread)
			.meat(meat)
			.vegetable(vegetable)
			.sauce(sauce)
			.build();
	}
}
```
- 이건 생성자 주입. 생성자를 통해 의존성 주입받음. 이외에도 수정자(setter) 주입도 있음.
- 위에서 보면 WheatBread, Beef, Lettuce, TomatoSauce 같은 구체적 클래스에 의존하지 않게 되어, 의존성은 10개에서 6개로 줄어듦
- 그럼 Food, Hamburger, Bread, Meat, Vegetable, Sauce는 괜찮은가?
- 보편적으로 괜찮아. 의존성을 반으로 줄였잖아 한잔해~
- 의존성 제거가 아닌 의존성 약화가 바로 의존성 주입.
- 개수 뿐만 아니라 강한 의존도 생기지도 않도록.

#### new 사용 자제
new 자체가 하드코딩.
- Meat meat = new Beef();를 하면 Meat라는 추상 클래스를 사용함에도 반드시 소고기만 사용할 수 있음
- new를 사용하지 말라는 것이 아니라, 구현 객체가 인스턴스화되는 시점을 최대한 뒤로 미루라는 것.
- 의존성 주입은 Autowired 애너테이션으로만 사용할 수 있는 게 아니다. 그냥 필요한 객체나 값을 외부에서 넣어주면 의존성 주입.

### 의존성 역전(Dependency Inversion, DIP)
SOLID 5번째 원칙, 가장 중요한 원칙.
- Restaurant 클래스가 HambugerChef 클래스를 통해 햄버거를 만든다 가정.
- Restuarant -- HamburgerChef 방향(의존성)
- 이때 Chef라는 인터페이스 만들고 Restaurant 클래스가 Chef 인터페이스에서 의존하도록 바꾸면, HamburgerChef 클래스도 Chef 인터페이스에 의존.
- Restaurant -- Chef <- HamburgerChef와 같이 화살표가 감.
- 즉, HamburgerChef 클래스로 들어오던 화살표가 Chef로 나가는 방향으로 바뀜(Chef 인터페이스에 의존)
- 이후 PastaChef 클래스를 추가해도 Chef의 코드 수정 필요 없이 구현만 하면 됨.
- 이게 바로 의존성 역전.
- 세부 사항에 의존하지 않고 정책에 의존하도록 코드를 작성하라!
#### 의존성 역전은 경계를 만든다?
- Rest -- Ch/ef <- HamburgerChef
- 경계를 기준으로 모듈을 나누면, 상하 관계를 파악할 수 있음.
- Restaurant, Chef 포함한 모듈은 상위, hamburgetChef 모듈은 하위 모듈이 됨.
- 하위 모듈은 상위 모듈에 의존하지만 상위 모듈은 하위 모듈에 절대 의존하지 않음. 
- 따라서 한식당으로 바꿔 KoreanChef로 변경해도 상위 모듈은 재사용하면 되는 것. 마치 플러그인과도 같음.

### 의존성 역전과 스프링
스프링은 의존성 주입을 지원하지만 의존성 역전은 지원하지 않음. 따라서 개발자가 능동적으로 구현해야
### 의존성 강조 이유?
코드를 변경하거나 확장할 때 영향받는 범위를 최소화할 수 있어야 하기 때문ㅇ
#### 의존성 전이
한 컴포넌트가 변경되거나 영향을 받으면 관련된 다른 컴포넌트에도 영향을 주고, 이 과정이 반복
- 의존성은 화살표의 역방향으로 전이된다. 책 129pg
#### 순환참조?
컴포넌트의 영향 범위를 더 넓게 만든다.
- 변경으로 인한 영향 범위를 축소시켜야 하는 원칙에 맞지 않다.
- 순환 참조를 하는 두 개 이상의 클래스는 사실상 같은 클래스라고 봐야 한다(영향받는 컴포넌트의 범위가 같음)
## SOLID와 객체지향
- SOLID를 추구하는 것이 객체지향으로 이어지는 것은 아니다.
- 따라서, 객체지향의 본질을 이해하고 구현한 이후 SOLID가 추구하는 목표 즉 높은 응집도와 낮은 결합도를 적용해보려 노력해야.
## 디자인 패턴
소프트웨어 설계를 하면서 자주 만나게 되는 문제 상황을 정의하고, 이를 해결할 수 이쓴ㄴ 모범 설계 사례를 모아놓은 것
- 디자인 패턴을 따로 공부하려고 하기보단, 먼저 의존성을 고민해보며 어떤 디자인 패턴이 어떤 상황에서 어떤 문제를 해결하는지 이해하는 것이 바람직하다.



