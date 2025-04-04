# Kotlin

# 1. Iterator

#### Iterator
> Kotlin의 Iterator는 `List`, `Set`, `Map` 등의 Collection 요소들을 순회할 수 있도록 한다.
- `iterator()`: `Iterator()` 함수를 호출함으로써 `Iterable<T>`의 상속자에 대한 Iterator를 얻을 수 있다.
- Iterator를 부를 시에는 Collection의 첫 번째 요소를 가리킨다.
- 주의할 사항은 마지막 요소를 넘어가면 더이상 Iterator는 쓸 수 없다는 점이다. 따라서, 새로운 Iterator를 생성해야한다.

Iterator의 Interface는 다음과 같이 정의되어있다.
```kotlin
public expect interface Iterator<out T> {
    /**
     * Returns the next element in the iteration.
     *
     * @throws NoSuchElementException if the iteration has no next element.
     */
    public operator fun next(): T

    /**
     * Returns `true` if the iteration has more elements.
     */
    public operator fun hasNext(): Boolean
}
```
- `next()`: 만약 존재하는 경우 다음 요소를 반환한다.
- `hasNext()`: 다음 요소가 존재하는지 여부를 반환한다.
<br>
# 2. For Loop & While Loop

```kotlin
for (i in 1..10) print(i)
```
위 For Loop는 `[1, 10]`까지 순회하며 값을 출력한다.

#### IntRange
> `Int` Type의 값의 범위를 나타내는 클래스이다.
- 위의 `1..10`에 해당하는 Data Type이다.
- `IntProgression`을 상속한다.
- `Int` 말고도 `Long`, `Char`에 대한 Progression 또한 수행할 수 있다.

IntRange의 클래스 함수는 다음과 같이 정의된다.
```kotlin
class IntRange(
	start: Int,
	endInclusive: Int
) : IntProgression, ClossedRange<Int>, OpenEndRange<Int>
```

위에서 제시된 For Loop Code를 While Loop으로 변환하면 다음과 같이 쓸 수 있다.
```kotlin
val range = 1..10
val iterator = range.iterator()

while(iterator.hasNext()) {
	print(iterator.next())
}
```
- `IntRange`는 `Iterator()`를 구현하여 `Iterator<Int>`를 반환한다.
- `Iterator()` Method를 호출하면 `IntProgressionIterator`가 생성되어 값을 순회할 수 있다.
<br>
# 3. `forEach()`

#### `forEach()`
> Sequencial Data에 대한 확장함수로, 기존의 For Loop을 내부 Iteration으로 바꾸어 가독성을 높인 함수이다.
- 위 Iterator 설명에서 언급한 `Iterable<T>`를 확장한 함수이기에 `List`, `Set` 등의 Collection에서 사용 가능하다.

`forEach()` 함수는 다음과 같이 정의된다.
```kotlin
public inline fun <T> Iterator<T>.forEach(operation: (T) -> Unit): Unit {
    for (element in this) operation(element)
}
```
- 이와 같이 `forEach()` 함수는 고차 함수이기에 `break`또는 `continue`를 상식대로 사용하면 안된다. 따라서 이럴 경우 그냥 For Loop을 사용하는 게 더 편할 수도 있다.
	- `forEach()`에서 `continue` 효과 내기: `return@forEach` 사용
	- `forEach()`에서 `break` 효과 내기: `forEach` Flow에 해당하는 부분을 임의의 `func` 함수로 감싼 뒤 `return@func` 사용