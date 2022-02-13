## 재사용: 상속보단 조립

### [1]. 상속과 재사용

### 상속을 통한 재사용의 단점

#### 1.상위 클래스 변경의 어려움

- 상속 계층을 따라 상위 클래스의 변경이 하위 클래스의 영향을 준다.

  최악의 경우, 상위 클래스의 변화가 모든 하위 클래스에 영향을 줄 수 있음

#### 2.클래스의 불필요한 증가

- 유사한 기능을 확장하는 과정에서 클래스의 개수가 불필요하게 증가한다.

  다중 상속이 불가능하기 때문에 구현된 클래스들이 있음에도 불구하고, 다시 구현을 해야할 수 있다.

  그로 인해 클래스의 불필요한 증가로 이어진다.

#### 3.상속의 오용

- 상속 자체를 잘못 사용할 수 있음을 의미한다.

```kotlin
class Container(
    private val maxSize: Int
): ArrayList<Luggage>() {
    private var currentSize: Int = 0
    
    fun put(lug: Luggage) {
        if(!canContain(lug)) throw NotEnoughSpaceException()
        super.add(lug)
        currentSize  += lug.size
    }
    
    fun extract(lug: Luggage) {
        super.remove(lug)
        currentSize -= lug.size
    }
    
    fun canContain(lug: Luggage): Boolean = maxSize >= currentSize + lug.size
}

fun main() {
    val luggage = Luggage(3)
    val container = Container(5)
    if(container.canContain(luggage)) container.put(luggage) // container.add(luggage)
}
```

- 개발자는 container에 put을 사용을 위해서 만들었지만, 잘못된 방법으로 add을 사용해 상속의 잘못된 방법으로 활용이 될 수 있다.

### [2]. 조립을 이용한 재사용

- `객체 조립(composition)` 은 여러 객체를 묶어서 더 복잡한 기능을 제공하는 객체를 만들어내는 것

- OOP에서 객체 조립은 필드에서 다른 객체를 참조하는 방식을 의미한다.

- 객체 조립을 통한 재사용으로 상속을 통한 재사용의 문제점을 해소할 수 있다.

  구현된 클래스들이 있다면, 그 내부에 필드로 객체를 참조해 불필요한 증가를 막을 수 있다.
  
```kotlin
class Container (
  private val maxSize: Int
) {
    private val luggages = ArrayList<Luggage>()
    var currentSize: Int = 0

    fun put(lug: Luggage) {
        if (!canContain(lug)) throw NotEnoughSpaceException()
        luggages.add(lug)
        currentSize += lug.size
    }

    fun extract(lug: Luggage) {
        luggages.remove(lug)
        this.currentSize -= lug.size
    }

    fun canContain(lug: Luggage): Boolean {
        return maxSize >= currentSize + lug.size
    }
}
```

#### 상속은 언제 사용하나?

- 상속은 재사용이라는 관점이 아닌 기능의 확장이라는 관점에서 적용해야 한다.

  상속을 위해서는 IS-A 관계가 성립되어야 한다.

### 참조

[링크 자료](https://jojoldu.tistory.com/412)
