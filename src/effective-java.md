# Effective-java
## 객체 생성과 파괴
* 객체를 생성하는 다양한 방법과 파괴 전에 수행해야 할 정리 작업을 관리하는 방법

### 아이템 1. 생성자 대신 정적 팩토리 메서드를 고려하라
* 핵심정리
  * 장점
    * 이름을 가질 수 있다(동일한 시그니처의 생성자를 두개 가질 수 없다.)
    * 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.(Boolean.valueOf)
    * 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다(인터페이스 기반 프레임워크, 인터페이스에서 정적 메소드)
    * 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.(EnumSet)
    * 정적 팩토리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.(서비스 제공자 프레임워크)
  * 단점
    * 상송을 하려면 public이나 protected 생성하기 


### 정적 팩토리 메서드의 첫번째 장점!!! 
#### - 이름을 가질 수 있다
* 한 클래스에 시그니처가 같은 생성자가 여러 개 필요할 것 같으면, 생성자를 정적 팩터리 메서드로 바꾸고  
  각각의 차이를 잘 드러내는 이름을 지어주자!!!

### Sample Code
```java
public class Order {

	private boolean prime;

	private boolean urgent;

	private Product product;

  public Order(Product product, boolean prime) {
    this.prime = prime;
    this.product = product;
  }
}
```

* 위 코드에서 prime대신 urgent를 받는 생성자를 추가로 선언하면 컴파일 에러가 발생한다.
  * 생성자의 시그니처는 받는 파라미터 타입까지 본다.   
  완전하게 동일한 시그니처의 생성자는 같이 있을 수 없다
  
```java
public class Order {
  ...
  
  public Order(Product product, boolean prime) {
    this.prime = prime;
    this.product = product;
  }

  public Order(Product product, boolean urgent) {
    this.urgent = urgent;
    this.product = product;
  }
}
```

* 이 문제를 해결하기 위해 정적 팩토리 메소드를 활용하자!!!!
* primeOrder와 urgentOrder 처럼 (만들어지는 객체의 특징을)메소드 명으로 의도를 명확하게 나타낼 수 있다.

```java

public class Order {
  ...
  public static Order primeOrder(Product product) {
    Order order = new Order();
    order.prime = true;
    order.product = product;
    return order;
  }
  
  public static Order urgentOrder(Product product) {
    Order order = new Order();
    order.urgent = true;
    order.product = product;
    return order;
  }
}

```

### 정적 팩토리 메서드의 두번째 장점!!! 
#### - 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.
* 인스턴스의 생성을 통제할 수 있다!

```java
public class Settings {
  private boolean useAutoSteering;
  private boolean useABS;
  private Difficulty difficulty;

  public static void main(String[] args) {
    System.out.println(new Settings());
    System.out.println(new Settings());
    System.out.println(new Settings());
  }
}
```
* 위 처럼 생성자를 사용하면 매번 새로운 인스턴스가 생성된다  

![image](https://user-images.githubusercontent.com/60100532/208928268-d06ea596-1b77-4700-9369-c2c74c68a4aa.png)
* 하지만 우리는 가끔 인스턴스를 매번 새로 만들지   
  아니면 특정 경우에만 생성할지 등등   
  인스턴스를 생성하는 방법을 통제할 필요가 생길 때가 있다.
* 생성자가 있다면 통제가 불가능하다!  
  (어디서든 생성자를 호출해서 새로운 인스턴스를 만들 수 있다.)

* 만약에 우리가 위의 Settings라는 인스턴스를 오직 1개만 만들어서 사용해야 한다면
* 그때 정적 팩토리 메소드를 통해서 통제할 수 있다.  


```java
public class Settings {
  private boolean useAutoSteering;
  private boolean useABS;
  private Difficulty difficulty;
  
  private Settings() {
  }

  private static final Settings SETTINGS = new Settings();
  
  public static Settings newInstance() {
    return SETTINGS;
  }
}
```
1. 기본 생성자를 private으로 선언해 외부에서 아무도 호출하지 못하게 한다.
2. 미리 Settings를 만들어 놓고
3. newInstance()라는 메소드로 정적 팩토리 메소드를 선언해 미리 생성해놓은 Settings를 return한다. 
> 이렇게 하면 외부에서 Settings인스턴스를 가져갈 수 있는 방법이 오로지 newInstance라는 정적 팩토리 메소드를 통해서만 가져갈 수 있다.
> 

* example : Boolean.valueOf(false);   
  
![image](https://user-images.githubusercontent.com/60100532/208933098-071050e9-ba2a-4af8-835f-bb14306f131d.png)

* 디자인 패턴 
* 플라이웨이트 (flyweight) 패턴
* 자주 변하는 속성(또는 외적인 속성, extrinsit)과 변하지 않는 속성(또는 내적인 속성, intrinsit)을 분리하고 `케싱` 재사용하여 메모리 사용을 줄일 수 있다.

![image](https://user-images.githubusercontent.com/60100532/200568408-76c19d4e-f254-4371-ba40-a16b7c2d6d7a.png)
* 케싱 개념 미리 가지고있다가 필요할때 꺼내 준다 -> 정적 팩토리 메소드 패턴과 유사.


### 정적 팩토리 메서드의 세,네 번째 장점!!! 
#### - 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다. 
#### - 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
* 인터페이스를 사용할 수 있다.
* 정적 팩토리 메소드 리턴 타입에는 인터페이스를 선언하고 실제 리턴해주는 인스턴스는 인터페이스의 구현체로 바꿔 리턴하거나 
* 클래스를 리턴 타입으로 선언해 그 클래스의 하위 클래스를 리턴해 줄 수 있어 굉장한 유연함이 생긴다.  
  
  
* 기존에 생성자(생성자는 해당하는 클래스의 인스턴스만 만들어 준다.)를 사용할때는   
  KoreanHelloService나 EnglishHelloService의 선언한 생성자가 return해 주는 인스턴스만! 가져올 수 있었지만.
* 정적 팩토리 메소드를 사용하게 된다면   
  정적 팩토리 메소드의 반환타입에 호환 가능한 다른 타입으로 return해 줄 수 있다.       
  

<br />    

#### 아래 코드를 보자.
* HelloServiceFactory의 of 라는 정적 팩토리 메소드에서   
HelloService 인터페이스를 return하도록 선언했지만 
* 전달 받은 변수에 따라서 그 구현체인 KoreanHelloService의 인스턴스나 
* EnglishHelloService의 인스턴스로 return해 줄 수 있다. 

```java
public class HelloServiceFactory{
  public static HelloService of(String lang) {
    if (lang.equals("ko")) {
      return new KoreanHelloService();
    } else {
      return new EnglishHelloService();
    }
  }
}
public interface HelloService {

  String hello();
}

public class KoreanHelloService implements HelloService {
	
	@Override
    public String hello() {
      return 안녕하세요;
    }
}
public class EnglishHelloService implements HelloService {
	
	@Override
    public String hello() {
      return "hello";
    }
}


```
* 정적 팩토리 메소드를 사용하면  
  아래 코드처럼 구체적인 타입을 클라이언트로부터 숨길 수 있다.
```java
public static void main(String[]args){
    HelloService ko = HelloServiceFactory.of("ko");        
}
```

* 자바 8버전 부터는 HelloServiceFactory의 of와 같은 스태틱 메소드를 인터페이스에 선언할 수 있어 불필요한 Factory클래스의 선언을 줄일 수 있다..


### 정적 팩토리 메서드의 다섯 번째 장점!!!
#### - 정적 팩토리 메소드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
* 서비스 제공자 프레임워크(service provider framework)를 만드는 근간이 됨

* 현재 HelloService에 대한 구현체가 없는 상황

```java
import java.util.Optional;
import java.util.ServiceLoader;

public interface HelloService {
  String hello();
}

public class HelloServiceFactory {
  public static void main(String[] args) {
    ServiceLoader<HelloService> load = ServiceLoader.load(HelloService.class);// ServiceLoad java가 기본으로 제공하는 정적 팩토리 메소드
    Optional<HelloService> helloService = load.findFirst();
    helloService.ifPresent(h -> System.out.println(h.hello()));
  }
}
```

  
___
___



### 정적 팩토리 메서드의 첫번째 단점!!!
#### - 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.
* 정적 팩토리 메서드만을 사용하게 만든 클래스가 있다면 그렇게 하기 위해 생성자를 `private`으로 선언해야 한다.
* 이말은 즉 해당 클래스는 상속을 허용하지 않는다.
* 하지만 이는 컴포지션(delegate)을 사용하도록 유도해 굳이 상속없이 해당클래스의 내부 기능들을 사용할 수 있다
* 이점에서 오리혀 장점으로 받아들일 수도 있다.

```java
public class Settings {
  private boolean useAutoSteering;
  private boolean useABS;
  private Difficulty difficulty;
  
  private Settings() {
  }

  private static final Settings SETTINGS = new Settings();
  
  public static Settings newInstance() {
    return SETTINGS;
  }
}

public class AdvancedSettings {
  Settings settings;
}
```
 
* 정적 팩토리 메서드를 제공한다고 해서 꼭 생성자를 `private`으로 만들 필요는 없다.
* `new ArrayList<>()`, `List.of()`


### 정적 팩토리 메서드의 두번째 단점!!!
#### - 정적 팩토리 메소드는 프로그래머가 찾기 어렵다.
* 자바독에 메소드는 생성자처럼 API설명에 명확히 드러나지 않는다. 
* 해당 클래스에 선언된 메소드가 많다면, 정적 팩토리 메소드를 찾기는 더욱 어려워진다.
* 이단점을 완화 하기 위해 API문서를 잘 작성하고, 정적 팩토리 메소드 이름도 널리 알려진 규약을 따라 짓는다.


```java
/**
 * 이 클래스의 인스턴스는 #getInstance()를 통해 사용한다.
 * @see #getInstance()
 */
public class Settings {
  ...
  private Settings() {
  }

  private static final Settings SETTINGS = new Settings();
  
  public static Settings getInstance() {
    return SETTINGS;
  }
}
```
___
___

### 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라
* 핵심정리
  * 정적 팩토리와 생성자에 선택적 매개변수가 많을 때 고려할 수 있는 방안
    * 대안1: 점층적 생성자 패턴 또는 생성자 체이닝
      * 매개변수가 늘어나면 클라이언트 코드를 작서하거나 읽기 어렵다.
    * 대안2:자바빈즈 패턴
      * 완번한 객체를 만들려면 메서드를 여러번 호출해야한다.(일관성이 무너진 상태가 될 수도 있다.)
    * 클래스를 불변으로 만들 수 없다. 


### 점층적 생성자 패턴
### Sample Code
```java
public class NutritionFacts {
    private final int servingSize;  // (mL, 1회 제공량)     필수
    private final int servings;     // (회, 총 n회 제공량)  필수
    private final int calories;     // (1회 제공량당)       선택
    private final int fat;          // (g/1회 제공량)       선택
    private final int sodium;       // (mg/1회 제공량)      선택
    private final int carbohydrate; // (g/1회 제공량)       선택

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
//        this.servingSize = servingSize;
//        this.servings = servings;
//        this.calories = 0;
//        this.fat = 0;
//        this.sodium = 0;
//        this.carbohydrate = 0;
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories) {
        this(servingSize, servings, calories, 0);
//        this.servingSize = servingSize;
//        this.servings = servings;
//        this.calories = calories;
//        this.fat = 0;
//        this.sodium = 0;
//        this.carbohydrate = 0;
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
//        this.servingSize = servingSize;
//        this.servings = servings;
//        this.calories = calories;
//        this.fat = fat;
//        this.sodium = 0;
//        this.carbohydrate = 0;
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
//        this.servingSize = servingSize;
//        this.servings = servings;
//        this.calories = calories;
//        this.fat = fat;
//        this.sodium = sodium;
//        this.carbohydrate = 0;
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize  = servingSize;
        this.servings     = servings;
        this.calories     = calories;
        this.fat          = fat;
        this.sodium       = sodium;
        this.carbohydrate = carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola =
                new NutritionFacts(10, 10);
    }
    
}
```
 
* 위 코드는 생성자도 많지만 생성자에 넘겨주는 파라미터도 많다.
* 필수적인 필드값은 객체를 만들때 받아서 만들수있도록 강제하기 위해 생성자에 넘겨주는게 좋다.
* 위 예시코드처럼 옵셔널한 필드값이 많으면 점층적 생성자 패턴을 고려해보자.
* 위 코드에서 문제점은 생성자로 받는 필드가 전부 int 값 이다. 즉 생성자로 넘긴값이 어떤 파라미터로 들어가는지 알기 힘들다.

### 자바빈즈 패턴
### Sample Code
```java
public class NutritionFacts {
    // 필드 (기본값이 있다면) 기본값으로 초기화된다.
    private int servingSize  = -1; // 필수; 기본값 없음
    private int servings     = -1; // 필수; 기본값 없음
    private int calories     = 0;
    private int fat          = 0;
    private int sodium       = 0;
    private int carbohydrate = 0;
    private boolean healthy;

    public NutritionFacts() { }

    public void setServingSize(int servingSize) {
        this.servingSize = servingSize;
    }

    public void setServings(int servings) {
        this.servings = servings;
    }

    public void setCalories(int calories) {
        this.calories = calories;
    }

    public void setFat(int fat) {
        this.fat = fat;
    }

    public void setSodium(int sodium) {
        this.sodium = sodium;
    }

    public void setCarbohydrate(int carbohydrate) {
        this.carbohydrate = carbohydrate;
    }

    public void setHealthy(boolean healthy) {
        this.healthy = healthy;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts();
        cocaCola.setServingSize(240);
        cocaCola.setServings(8);

        cocaCola.setCalories(100);
        cocaCola.setSodium(35);
        cocaCola.setCarbohydrate(27);
    }
}
```
* 자바빈즈 패턴은 매개변수가 없는 생성자로 객체를 만들 후 Setter를 통해서 원하는 매개변수의 값을 설정하는 방식이다.
* 이전 점층적 생성자 패턴과 다르게 객체의 생성이 매우 단순해졌다.
* 점층적 생성자 패턴에서 보였던 단점은 자바빈즈 패턴에서 보이지 않는다.
* 코드가 길어지긴 했지만 인스턴스를 만들기가 쉬워졌고, 그 결과 코드를 읽기 더 쉬워졌다.
* 단점은 자바빈즈 패턴에서는 객체 하나를 만드려면 메서드를 여러개 호출해야한다.
* 또한 필수 필드값을 강제할 수 없어 객체가 완전히 생성되기 전까지 일관성(Consistency)이 무너진 상태에 놓이게 된다.
* 마지막으로 자바 빈즈 패턴에서는 클래스를 분변으로 만들 수 없다.

### 빌더 패턴
### Sample Code
```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static void main(String[] args) {
        NutritionFacts cocaCola = new Builder(240, 8)
                .calories(100)
                .sodium(35)
                .carbohydrate(27).build();
    }

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories      = 0;
        private int fat           = 0;
        private int sodium        = 0;
        private int carbohydrate  = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings    = servings;
        }

        public Builder calories(int val)
        { calories = val;      return this; }
        public Builder fat(int val)
        { fat = val;           return this; }
        public Builder sodium(int val)
        { sodium = val;        return this; }
        public Builder carbohydrate(int val)
        { carbohydrate = val;  return this; }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize  = builder.servingSize;
        servings     = builder.servings;
        calories     = builder.calories;
        fat          = builder.fat;
        sodium       = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
```
* 점층적 생성자 패턴과 자바빈즈 패턴의 장점만 취했다.
* 점층적 생성자 패턴의 안정성과 자바 빈즈 패턴의 가독성을 겸비했다.
* 클라이언트는 필요한 객체를 직접 만드는 대신, 필수 매개변수만으로 생성자를 호출해 빌더 객체를 얻고
* 그 이후 빌더객체가 제공하는 일종의 세터 메서드 들로 원하는 매개변수들을 설정한다.
* 마지막으로 매개변수가 없는 build메서드를 호출해 우리가 피룡한 객체를 얻는다.


### 계층형 빌더 패턴
### Sample Code
```java
// 코드 2-4 계층적으로 설계된 클래스와 잘 어울리는 빌더 패턴 (19쪽)

// 참고: 여기서 사용한 '시뮬레이트한 셀프 타입(simulated self-type)' 관용구는
// 빌더뿐 아니라 임의의 유동적인 계층구조를 허용한다.

public abstract class Pizza {
    public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        // 하위 클래스는 이 메서드를 재정의(overriding)하여
        // "this"를 반환하도록 해야 한다.
        protected abstract T self();
    }
    
    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone(); // 아이템 50 참조
    }
}
```
```java
// 코드 2-6 칼초네 피자 - 계층적 빌더를 활용한 하위 클래스 (20~21쪽)
public class Calzone extends Pizza {
    private final boolean sauceInside;

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauceInside = false; // 기본값

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        @Override public Calzone build() {
            return new Calzone(this);
        }

        @Override protected Builder self() { return this; }
    }

    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }

    @Override public String toString() {
        return String.format("%s로 토핑한 칼초네 피자 (소스는 %s에)",
                toppings, sauceInside ? "안" : "바깥");
    }
}
```
```java
// 코드 2-5 뉴욕 피자 - 계층적 빌더를 활용한 하위 클래스 (20쪽)
public class NyPizza extends Pizza {
    public enum Size { SMALL, MEDIUM, LARGE }
    private final Size size;

    public static class Builder extends Pizza.Builder<NyPizza.Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override public NyPizza build() {
            return new NyPizza(this);
        }

        @Override protected Builder self() { return this; }
    }

    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }

    @Override public String toString() {
        return toppings + "로 토핑한 뉴욕 피자";
    }
}
```
* 각 하위 클래스(NyPizza, Calzone)의 빌더가 정의한 build 메서드는 해당하는 구체 하위 클래스를 반환하도록 언선한다.
* NyPizza.Builder는 NyPizza를 반환하고, 
* Calzone.Builder는 Calzone를 반환한다.
* 하위 클래스의 메서드가 상위 클래스의 메서드가 정의한 반환 타입이 아닌,
* 그 하위타입을 반환하는 기능을 공변 반환 타이핑(covariant return typing)이라 한다.
* 이 기능을 이용하면 클라이언트가 형변환에 신경 쓰지 않고도 빌더를 사용할 수 있다.
