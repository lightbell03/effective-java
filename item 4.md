# item 4 - 인스턴스화를 막으려거든 private 생성자를 사용하라

## 인스턴스화를 막으려거든 private 생성자를 사용하라

객체지향적으로 바라볼때 정적 메서드와 정적 필드만을 담은 클래스를 만들어 남용하는 것은 좋지 않지만, 때에 따라서 필요한 경우가 있다.

1. java.lang.Math 와 java.util.Arrays 처럼 기본 타입 값이나 배열 관련 메서드들을 모아놓는 경우
2. java.util.Collections 처럼 특정 인터페이스를 구현하는 객체를 생성해주는 정적 메서드(혹은 패토리)를 모아놓는 경우
3. final 클래스와 관련한 메서드들을 모아놓는 경우
→ final 클래스를 상속해서 하위 클래스에 메서드를 넣는 것은 불가능 하기때문

이처럼 정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한 것이 아니다. 하지만 **생성자를 명시하지 않으면 컴파일러가 자동으로 기본 생성자를 만들**어준다.

```java
public class UtilityClass {

    public static void func() {}
}

...
UtilityClass utilityClass = new UtilityClass();
...
```

매개변수를 사용하지 않는 기본 생성자가 만들어지고 사용자는 생성자가 자동으로 생성된 것인지 아니면 만들어진 생성자인지 알지 못한다.

이를 해결하기 위해 인스턴스를 만들 수 없는 추상클래스로 만드는 것은 좋지않다. **추상 클래스로 만드는 것으로는 인스턴스화를 막지 못한다**. 하위 클래스를 만들어 인스턴스화를 하면 되면 되기 때문이다. 혹은 이 추상클래스를 본 사용자는 상속해서 쓰라는 것으로 오해할 수 있어 더 큰 문제가 생길 수 있다.

### private 생성자를 추가해 인스턴스화를 막기

```java
public class UtilityClass {
    // 기본 생성자가 만들어지는 것을 막는다(인스턴스화 방지용)
    private UtilityClass() {
        throw new AssertionError();
    }
		...
}
...
UtilityClass utilityClass = new UtilityClass(); // 에러 발생 (컴파일 타임에서 에러 발견)
...
```

명시적 생성자의 접근자가 private 이므로 클래스 바깥에서는 접근이 불가능하다. 그래도 클래스 안에서 실수로라도 생성자를 호출했을 경우를 대비해 에러를 발생해주는 코드를 적어준다.

이 방식은 상속도 불가능하게 하는 효과도 있다. 하위 클래스에서 상위 클래스의 생성자에 접근을 할 수 없으므로 하위 클래스를 만들 수가 없게 된다.

> 생성자가 분명 존재하는데 호출할 수 없어 직관적이지 않으므로 주석을 달아줘 사용자가 알 수 있게 해주자.
>