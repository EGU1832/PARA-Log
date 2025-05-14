20221623 최서임

# 1. Properties

## 1-A.
  
`val`과 `const val`의 차이를 설명하시요.

- `val`: 런타임 시점에 값을 할당한다.
- `const val`: 컴파일 시간에 값을 할당한다. 반드시 기본 자료형이나 문자열로 할당되어야 한다.
  
## 1-B.
  
컴파일 시간에 값이 결정되어야 하기 때문에 오직 top-level, object, companion object 멤버여야만 클래스 초기화와 무관하게 접근이 가능하다.
  
# 2. Sequences
  
Kotlin `Sequence`는 `Iterator`를 통해 각 element에 순차적으로 operation을 수행할 수 있는 type 중 하나로 terminal operation을 통해서만 evaluation이 시작되는 lazy 속성 을 가진다.
```kotlin
public interface Sequence<out T> {
	public operator fun iterator(): Iterator<T>
}
```
  
## 2-A.
  
`Sequence`의 extension function `map`은 `TransformingSequence`를 리턴하는 intermediate operation이다. `map` function과 `TransformingSequence` class의 소스 코드를 예시하여 intermediate operation의 동작방식을 상세 설명하시요.
  
```kotlin
public fun <T, R> Sequence<T>.map(transform: (T) -> R): Sequence<R> {
    return TransformingSequence(this, transform)
}

internal class TransformingSequence<T, R>  
constructor(private val sequence: Sequence<T>, private val transformer: (T) -> R) : Sequence<R> {  
    override fun iterator(): Iterator<R> = object : Iterator<R> {  
        val iterator = sequence.iterator()  
        override fun next(): R {  
            return transformer(iterator.next())  
        }  
  
        override fun hasNext(): Boolean {  
            return iterator.hasNext()  
        }  
    }  
  
    internal fun <E> flatten(iterator: (R) -> Iterator<E>): Sequence<E> {  
        return FlatteningSequence<T, R, E>(sequence, transformer, iterator)  
    }  
}
```
- 각 원소는 `.map()`을 호출할 때 변환되지 않고 `.first()`등의 terminal 함수가 실행될 때 순차적으로 변환된다.
  
## 2-B. 
  
`Sequence` interface의 terminal operation 중 하나인 `first` function의 소스코드를 예시하고 intermediate operation과 비교하여 `Sequence`의 lazy evaluation 구현 방식을 구체적으로 기술하시요.
  
```kotlin
public fun <T> Sequence<T>.first(predicate: (T) -> Boolean): T {
    for (element in this) if (predicate(element)) return element
    throw NoSuchElementException("No element matching predicate was found.")
}
```
- `first()`: element를 하나씩 평가하며, 조건을 만족하면 즉시 반환한다.
- `map()`: 연산을 보류하고 `first()`호출 시에만 각 원소에 `transform()`을 적용한다.
  
# 3. Generics
  
Kotlin `Collection` 중 generic interface `List`와 `MutableList` 소스코드를 참조하여
  
## 3-A.
  
각각의 type parameter variance를 확인하고 서로 다른 variance를 가지는 이유를 설명하시요.
  
```kotlin
public expect interface List<out E>
public expect interface MutableList<E> : List<E>, MutableCollection<E>
```
- `List`: 읽기 전용, `out` 사용 가능
- `MutableList`: 읽기 쓰기 모두 가능, 타입 안정성을 위해 invariant
  
## 3-B.
  
List의 method 중 선언된 type parameter variance에 위배되는 function의 사례 2~3 개를 제시하고, compile time error 회피를 위하여 사용된 방법을 설명하시요.
  
- `List<out T>`에선 `add()`와 같은 함수가 variance에 위배된다.
- e.g.
	- `plus(element: E): List<E>`: 새로운 리스트를 반환한다.
	- `filter(predicate: (E) -> Boolean): List<E>`: 원본이 변하지 않고 새 리스트를 생성한다.
- 모두 compile time error 회피를 위하여 원본을 변경하지 않고 새 인스턴스를 반환한다.
  
## 3-C.
  
B.에서 제시한 method가 variance 위배에도 불구하고 type safe 한 이유를 해당 method의 operation 속성을 예시하여 기술하시요.
  
e.g. `plus(element: E): List<E>`
```kotlin
fun <E> List<E>.plus(element: E): List<E> {
    val result = ArrayList(this)
    result.add(element)
    return result
}
```
- 원본 `List`는 변경되지 않고 복사된 `List`에만 영향을 주므로 type safe하다.
  
# 4. Type Projections
  
Type projection은 type variance를 얻기 위해 특정한 방식으로 제한된 type을 의미한다. In-projected, out-projected type의 속성을 이용하여 아래의 equivalence 관계가 성립함을 증명하시요.
  
## 4-A.
  
```
For Foo<out T: TUpper>, where T is a covariant type parameter with the upper bound TUpper, Foo<*> is equivalent to Foo<out TUpper>.
```
- `out`는 읽기 전용이며, `*`가장 범위가 넓은 type 허용이므로 `TUpper`사용이 안전하다.
  
## 4-B.
  
```
For Foo<in T>, where T is a contravariant type parameter, Foo<*> is equivalent to Foo<in Nothing>.
```
- `in`은 쓰기 전용이며 `*`는 아무 type도 보장하지 못하기에 `Nothing`만 허용하는 것이 안전하다.
  
## 4-C.
  
```
For Foo<T : TUpper>, where T is an invariant type parameter with the upper bound TUpper, Foo<*> is equivalent to Foo<out TUpper> for reading values and to Foo<in Nothing> for writing values.
```
- Invariant의 경우 읽기 시에는 가장 안전한 상한인 `TUpper`를 사용한다.
- 쓰기 시에는 `Nothing`만 넣는 것이 안전하다.
- 즉 `*`는 읽기/쓰기 별도로 분리해서 해석이 가능하다.
  
# 5. Scope Functions
  
아래는 scope function also를 이용한 one-line swap 코드이다.
```kotlin
var a = 1
var b = 2
a = b.also {b = a}
```
  
## 5-A.
  
Scope function also의 소스코드를 제시하여 함수의 동작과 대표적인 use case를 설명하시요.
```kotlin
public inline fun <T> T.also(block: (T) -> Unit): T {  
    contract {  
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)  
    }  
    block(this)  
    return this  
}
```
- 수신 객체를 인자로 넘겨 사이드 이펙트를 수행하고 원래 객체를 반환한다.
- 대표적 use case: Logging, Debugging 등 객체의 사이드 이팩트를 확인하거나 수신 객체의 프로퍼티에 데이터를 할당하기 전에 대핟 데이터의 유효성을 검사할 때
  
## 5-B.
  
위 코드에서 변수 값 swap이 가능한 이유를 A.의 소스코드 분석 내용과 function closure 개념을 통해 설명하시요.
  
```kotlin
var a = 1
var b = 2
a = b.also {b = a}
```
- `also` 내부에서 `b = a`가 먼저 수행되고 `also`는 `b`의 원래 값을 반환하므로 `a = b`가 된다.
- Function closure 덕분에 `a`의 기존 값이 `also` 내부에서 접근 가능하다.
  
## 5-C.
  
Scope function apply, let, run을 이용하여 변수 추가 없이 동일한 기능을 구현 하는 one-line swap 코드를 작성하시요.
  
```kotlin
// apply
a = b.apply { b = a }

// let
a = b.let { b = a; it }

// run
a = run { val temp = a; b.also { b = temp } }
```

![](../../Z.%20Docs/img/Pasted%20image%2020250507001944.png)