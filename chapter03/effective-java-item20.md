# Effective-java
## 클래스와 인터페이스
* 클래스와 인터페이스를 쓰기 편하고, 견고하며, 유연하게 만드는 방법

### 아이템 20. 추상 클래스보다 인터페이스를 우선하라.

### 핵심정리 - 인터페이스의 장점
* 자바 8부터 인터페이스도 디폴트 메서드를 제공할 수 있다. (완벽 공략 3)
* 기존 클래스도 손쉽게 새로운 인터페이스를 구현해 넣을 수 있다.
* 인터페이스는 믹스인(mixtin) 정의에 안성맞춤이다. (선택적인 기능 추가)
* 계층구조가 없는 타입 프레임워크를 만들 수 있다.
* 래퍼 클래스와 함께 사용하면 인터페이스는 기능을 향상 시키는 안전하고 강력한 수단이 된다. (아이템 18)
* 구현이 명백한 것은 인터페이스의 디폴트 메서드를 사용해 프로그래머의 일감을 덜어 줄 수 있다
                               
```java
/*
 * default method example
 */

public interface TimeClient {

	void setTime(int hour, int minute, int second);
	void setDate(int day, int month, int year);
	void setDateAndTime(int day, int month, int year,
		int hour, int minute, int second);
	LocalDateTime getLocalDateTime();

	static ZoneId getZonedId(String zoneString) {
		try {
			return ZoneId.of(zoneString);
		} catch (DateTimeException e) {
			System.err.println("Invalid time zone: " + zoneString + "; using default time zone instead.");
			return ZoneId.systemDefault();
		}
	}

	default ZonedDateTime getZonedDateTime(String zoneString) {
		return ZonedDateTime.of(getLocalDateTime(), getZonedId(zoneString));
	}
}
```

* default 메소드 추가로 기존 인터페이스를 구현한 클래스들의 컴파일 에러를 발생시키지 않고 기능을 추가할 수 있다.
                                 
### 핵심정리 - 인터페이스와 추상 골격(skeletal) 클래스
* 인터페이스와 추상 클래스의 장점을 모두 취할 수 있다.
 * 인터페이스 - 디폴트 메서드 구현
 * 추상 골격 클래스 - 나머지 메서드 구현
 * 템플릿 메서드 패턴
* 다중 상속을 시뮬레이트할 수 있다.
* 골격 구현은 상속용 클래스이기 때문에 아이템 19를 따라야 한다