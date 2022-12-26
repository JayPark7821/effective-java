# Effective-java
## 객체 생성과 파괴
* 객체를 생성하는 다양한 방법과 파괴 전에 수행해야 할 정리 작업을 관리하는 방법

### 아이템 5. 자원을 직접 명시하지 말고 `의존 객체 주입`을 사용하라

### 핵심정리
* 사용하는 자원에 따라 동작이 달라지는 클래스는 정적 유틸리티 클래스나 싱글톤 방식이 적합하지 않다.
* 의존 객체 주입이란 인스턴스를 생성할 때 필요한 자원을 넘겨주는 방식이다.
* 이 방식의 변형으로 생성자에 자원 팩터리를 넘겨줄 수 있다.
* 의존 객체 주입을 사용하면 클래스의 유연성, 재사용성, 테스트 용이성을 개선할 수 있다.

### static utility class Sample Code(Bad)
```java
public class SpellChecker {

    private static final Dictionary dictionary = new DefaultDictionary();

    private SpellChecker() {}

    public static boolean isValid(String word) {
        // TODO 여기 SpellChecker 코드
        return dictionary.contains(word);
    }

    public static List<String> suggestions(String typo) {
        // TODO 여기 SpellChecker 코드
        return dictionary.closeWordsTo(typo);
    }
}
``` 
* 위 샘플 코드는 static한 utility class이다.
* SpellChecker에서 Dictionary자원을 직접 명시하고 있기 때문에
* 위 클래스의 동작을 확인하려면 dictionary가 만들어질수 밖에 없다.
* 명시한 자원에따라서 동작이 바뀌어야 하는 경우에 자원을 직접 명시하지말고 의존 객체 주입을 사용하라.
* 위 샘플 코드에서 dictionary는 바뀔수 있다. 영어 사전이냐, 한국어 사전이냐에 따라 다르다.

### singleton utility class Sample Code(Bad)
```java
public class SpellChecker {

    private final Dictionary dictionary = new DefaultDictionary();

    private SpellChecker() {}

    public static final SpellChecker INSTANCE = new SpellChecker();

    public boolean isValid(String word) {
        // TODO 여기 SpellChecker 코드
        return dictionary.contains(word);
    }

    public List<String> suggestions(String typo) {
        // TODO 여기 SpellChecker 코드
        return dictionary.closeWordsTo(typo);
    }
}
```

> 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.
> 

### 의존객체 주입 Dependency Injection
```java
public class SpellChecker {

    private final Dictionary dictionary;

    public SpellChecker(Dictionary dictionary) {
        this.dictionary = dictionary;
    }

    public SpellChecker(Supplier<Dictionary> dictionarySupplier) {
        this.dictionary = dictionarySupplier.get();
    }

    public boolean isValid(String word) {
        // TODO 여기 SpellChecker 코드
        return dictionary.contains(word);
    }

    public List<String> suggestions(String typo) {
        // TODO 여기 SpellChecker 코드
        return dictionary.closeWordsTo(typo);
    }
}
```
* 의존 객체 주입은 인스턴스를 생성할 때 필요한 자원을 넘겨주는 방식이다.
* 위 방식과 같은 경우에는 Dictionary가 바뀐다 하더라도 SpellChecker를 수정할 필요가 없고 모든 코드를 재사용 할 수 있다.
* (Dictionary가 interface라는 가정일때)
* 위 방법을 사용하면 테스트하기도 편해진다.

> 핵심정리
> * 클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자우너이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다.  
>   이 자원들을 클래스가 직접 만들게 해서도 안 된다.  
>   대신 필요한 자원을 (혹은 그 자원을 만들어주는 팩터리를) 생성자에 ( 혹은 정적 팩터리나 빌더에) 넘겨주자.  
>   의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해준다.