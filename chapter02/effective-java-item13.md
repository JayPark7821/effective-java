# Effective-java
## 모든 객체의 공통 메서드
* Object가 제공하는 메서드를 언제 어떻게 재정의해야 하는가

### 아이템 13. clone 재정의는 주의해서 진행하라. 

### 핵심정리 - 애매모호한 clone 규약
* clone 규약
  * x.clone() != x 반드시 true
  * x.clone().getClass() == x.getClass() 반드시 true
  * x.clone().equals(x) true가 아닐 수도 있다.
* 불변 객체라면 다음으로 충분하다. 
  * Cloneable 인터페이스를 구현하고
  * clone 메서드를 재정의한다. 이때 super.clone()을 사용해야 한다.

### Sample Code
```java
 public static void main(String[] args) {
        PhoneNumber pn = new PhoneNumber(707, 867, 5309);
        Map<PhoneNumber, String> m = new HashMap<>();
        m.put(pn, "제니");
        PhoneNumber clone = pn.clone();
        System.out.println(m.get(clone));

        System.out.println(clone != pn); // 반드시 true
        System.out.println(clone.getClass() == pn.getClass()); // 반드시 true
        System.out.println(clone.equals(pn)); // true가 아닐 수도 있다.
    }
```

### 핵심정리 - 가변 객체의 clone 구현하는 방법
* 접근 제한자는 public 반환 타입은 자신의 클래스로 변경한다.
* super.clone()을 호출한 다음 필요한 필드를 수정한다.
  * 배열을 복제할 때는 배열의 clone 메서드를 사용하라.
  * 경우에 따라 final을 사용할 수 없을지도 모른다. 
  * 필요한 경우 deep copy를 해야한다. 
  * super.clone으ㅗ 객체를 만든 뒤, 고수준 메서드를 호출하는 방법도 있다. 
  * 오버라이딩 할 수 있는 메서드는 참조하지 않도록 조심해야 한다.
  * 상속용 클래스는 Cloneable을 구현하지 않는 것이 좋다. 
  * Cloneable을 구현한 스레드 안전 클래스를 작성할 때는 동기화를 해야 한다.
