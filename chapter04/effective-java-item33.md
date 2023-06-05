# Effective-java
## 제네릭
* 제네릭의 장점을 살리고 단점을 최소화하는 방법

### 아이템 33. 타입 안전 이종 컨테이너를 고려하라.

### 핵심정리 : 타입 토큰을 사용한 타입 안전 이종 컨테이너
* 타입 안전 이종 컨테이너 : 한 타입의 객체만 감을 수 있는 컨테이너가 아니라 여러 다른 타입 (이종)을 담을 수 있는 타입 안전한 컨테이너.
* 타입 토큰 : `String.class` 또는 `Class<String>`
* 타입 안전 이종 컨테이너 구현 방법 : 컨테이너가 아니라 "키"를 매개변수화 하라!


```java
public class Favorites {

    private Map<Class, Object> map = new HashMap<>();

    public void put(Class clazz, Object value) {
        this.map.put(Objects.requireNonNull(clazz), clazz.cast(value));
    }

    public Object get(Class clazz) {
        return this.map.get(clazz);
    }

    public static void main(String[] args) {
        Favorites favorites = new Favorites();
        favorites.put(String.class, "jay");
        favorites.put(String.class, 2);
        favorites.put(Integer.class, 2);
        favorites.put(Integer.class, "2");
    }
}
```

* class의 class는 제네릭 클래스이다.
* class에 비한정적 와일드 카드를 사용해서 여러 타입을 안전하게 담으려면 아래와 같이 수정하면 된다.

```java
public class Favorites {

	private Map<Class<?>, Object> map = new HashMap<>();

	public <T> void put(Class<T> clazz, T value) {
		this.map.put(Objects.requireNonNull(clazz), clazz.cast(value));
	}

	public <T> T get(Class<T> clazz) {
		return clazz.cast(this.map.get(clazz));
	}

	public static void main(String[] args) {
		Favorites favorites = new Favorites();
		favorites.put(String.class, "Jay");
		favorites.put(Integer.class, 2);
		Integer integer = favorites.get(Integer.class);
		String string = favorites.get(String.class);
		
	}

}

```  
* Class.cast 메서드 사용시 형변환이 가능한지 체크하고 케스트 하기 떄문에 waring이 발생하지 않는다.
![image](https://github.com/JayPark7821/effective-java/assets/60100532/077b33b3-0926-4e4d-810f-1447c2502a97)

* 취약점 1
```java

public static void main(String[] args) {
		Favorites favorites = new Favorites();
		favorites.put((Class)String.class, 1); // class 객체를 로타입으로 넘기면 타입 안정성을 보장할 수 있는 방법이 없다.
		  
		String string = favorites.get(String.class);
		
	}

```

* 취약점 2
```java
    public static void main(String[] args) {
        Favorites favorites = new Favorites();
        favorites.put(String.class, "jay");
        favorites.put(Integer.class, 2);

       favorites.put(List.class, List.of(1, 2, 3)); 
       favorites.put(List.class, List.of("a", "b", "c"));
        // List<Integer> 인지 List<String> 인지 구분할 수 없다.
        // List<Integer>.class 라는 리터럴은 존재하지 않는다.
    
       List list = favorites.get(List.class);
       list.forEach(System.out::println);
    }
```