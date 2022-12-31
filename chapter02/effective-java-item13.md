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
