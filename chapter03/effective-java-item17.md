# Effective-java
## 클래스와 인터페이스
* 클래스와 인터페이스를 쓰기 편하고, 견고하며, 유연하게 만드는 방법

### 아이템 17. 변경 가능성을 최소화 하라.

### 핵심정리 - 불변 클래스
* 불변 클래스는 가변 클래스보다 설계하고 구현하고 사용하기 쉬우며, 오류가 생길 여지도 적고 훨씬 안전하다.
* 불변 클래스를 만드는 다섯 가지 규칙
  * 객체의 상태를 변경하는 메서드를 제공하지 않는다.
  * 클래스를 확장할 수 없도록 한다.  - final class
  * 모든 필드를 final로 선언한다.
  * 모든 필드를 private으로 선언한다.
  * 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.


```java
public final class Person {

    private final Address address;

    public Person(Address address) {
        this.address = address;
    }

    public Address getAddress() {
        Address copyOfAddress = new Address();
        copyOfAddress.setStreet(address.getStreet());
        copyOfAddress.setZipCode(address.getZipCode());
        copyOfAddress.setCity(address.getCity());
        return copyOfAddress;
    }

    public static void main(String[] args) {
        Address seattle = new Address();
        seattle.setCity("Seattle");

        Person person = new Person(seattle);

        Address redmond = person.getAddress();
        redmond.setCity("Redmond");

        System.out.println(person.address.getCity());
    }
}

```
```java

public class Address {

    private String zipCode;

    private String street;

    private String city;

    public String getZipCode() {
        return zipCode;
    }

    public void setZipCode(String zipCode) {
        this.zipCode = zipCode;
    }

    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }
}

```

### 방어적으로 복사를 활용해 불변 클래스 만들어준다. 
```java
public final class Person {
  ...
  public Address getAddress() {
    Address copyOfAddress = new Address();
    copyOfAddress.setStreet(address.getStreet());
    copyOfAddress.setZipCode(address.getZipCode());
    copyOfAddress.setCity(address.getCity());
    return copyOfAddress;
  }
}
```
### 만약에 그냥 address를 넘겨주면
```java
public final class Person {
  ...
  public Address getAddress() {
    return address;
  }
}
```
* address.set000등의 세터로 Person의 정보가 바뀔수 있다.


### 핵심정리 - 불변 클래스의 장점과 단점
* 함수형 프로그래밍에 적합하다 ( 피연산자에 함수를 적용한 결과를 반환하지만 피연산자가 바뀌지는 않는다.) 
* 불변 객체는 단순하다.
* 불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요 없다.
* 불변 객체는 안심하고 공유할 수 있다. ( 상수, public static final)
* 불변 객체 끼리는 내부 데이터를 공유할 수 있다.
* 객체를 만들 때 불변 객체로 구헝하면 이점이 많다.
* 실패 우너자성을 제공한다.
* 단점) 값이 다르다면 반드시 별도의 객체로 만들어야 한다.
* "다단계 연산"을 제공하거나. "가변 동반 클래스"를 제공하여 대처할 수 있다.