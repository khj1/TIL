## `Objects.isNull()` vs `obj == null`

객체의 null 체크 시 `Objects.isNull()`와 `obj == null`는 무슨 차이가 있고 무엇을 사용하는게 좋을까?

### `Objects.isNull()`

결론부터 말하면 `Objects.isNull()`은 단순히 null 체크를 위해 만들어진 메서드가 아니다. **따라서 단순히 null 체크를 위해 `Objects.isNull`을 사용하는 것은 그 목적에 부합하지 않는다.**

`Objects.isNull()`의 내부 코드를 살펴보면 다음과 같다.

```java
/**
 * Returns {@code true} if the provided reference is {@code null} otherwise
 * returns {@code false}.
 *
 * @apiNote This method exists to be used as a
 * {@link java.util.function.Predicate}, {@code filter(Objects::isNull)}
 *
 * @param obj a reference to be checked against {@code null}
 * @return {@code true} if the provided reference is {@code null} otherwise
 * {@code false}
 *
 * @see java.util.function.Predicate
 * @since 1.8
 */
public static boolean isNull(Object obj) {
    return obj == null;
}
```

`Objects.isNull()`도 내부에서 `obj == null`을 사용하고 있다. 

또한 위 주석을 살펴보면 다음과 같은 내용을 확인할 수 있다.

> This method exists to be used as a `java.util.function.Predicate`, `filter(Objects::isNull)`

따라서 단순 객체의 null 체크를 위해선 `obj == null`을 사용하자.

## String의 null 체크

문자열의 `null`과 **빈 문자열**은 서로 다른 개념이다.

### 1. `String.isEmpty()`

```java
public boolean isEmpty() {
    return value.length == 0;
}
```

문자열의 길이를 계산한다. 즉 `null`은 체크하지 못하고 빈 문자열인지만 검증한다.

### 2. `String.isBlank()`

```java
public boolean isBlank() {
    return indexOfNonWhitespace() == length();
}

public static int indexOfNonWhitespace(byte[] value) {
    int length = value.length;
    int left = 0;
    while (left < length) {
        char ch = getChar(value, left);
        if (ch != ' ' && ch != '\t' && !CharacterDataLatin1.instance.isWhitespace(ch)) {
            break;
        }
        left++;
    }
    return left;
}
```

문자열이 빈 문자열인지 또는 공백인지 검증한다. `null`은 체크하지 못한다.

### 3. `StringUtills.hasText()`

```java
public static boolean hasText(@Nullable String str) {
    return (str != null && !str.isEmpty() && containsText(str));
}

private static boolean containsText(CharSequence str) {
    int strLen = str.length();
    for (int i = 0; i < strLen; i++) {
        if (!Character.isWhitespace(str.charAt(i))) {
            return true;
        }
    }
    return false;
}
```

문자열이 null인지, 빈 문자열인지, 공백인지 모두 체크한다.

상황에 따라 다르겠지만 **일반적으로 문자열을 검증하는 상황에선 `StringUtills.hasText()`가 가장 효과적이다.**


