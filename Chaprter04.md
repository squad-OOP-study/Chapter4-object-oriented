---

## 상속의 정의
상속(inheritance)이란 기존의 클래스에 기능을 추가하거나 재정의 하여 새로운
클래스를 정의하는 것을 의미  
이러한 상속은 캡슐화, 추상화와 더불어 객체 지향 프로그래밍을 구성하는 중요한 특징이다.  

상속을 이용하면 기존에 정의되어 있는 클래스의 모든 필드와 메소드를 물려받아, 새로운 클래스를 생성할 수 있다.
이때 기존에 정의되어 있던 클래스를 부모 클래스(parent class) 또는 상위 클래스(super class), 기초 클래스(base class)라고도 한다.
그리고 상속을 통해 새롭게 작성되는 클래스를 자식 클래스(child class) 또는 하위 클래스(sub class), 파생 클래스(derived class)라고 부른다.

<br>
<br/>

## 상속을 통한 재사용의 단점 3가지

### 상위 클래스 변경의 어려움
- 상속 계층을 따라 상위 클래스의 변경이 하위 클래스에 영향을 주기 때문에, 최악의 경우 상위 클래스의 변화가 모든 하위 클래스에 영향을 줄 수 있다.
- 이러한 영향으로 인해, 상속 구현으로 된 구조에선 (상속을 통한 재사용 구조에선), 상위 클래스의 변경이 어렵게 된다.
- 의존한다는 것은 의존하는 대상이 변경되면 영향을 받는다는 의미이다.

예시 
```kotlin
open class Korea {
    open fun priesident(name: String) {
        println("현재 대한민국의 대통령은 $name 입니다.")
    }
}

open class Seoul : Korea() {
    override fun priesident(name: String) {
        super.priesident(name)
    }
    open fun mayor(name: String) {
        println("현재 서울특별시의 시장은 $name 입니다.")
    }
}

class Gangnam : Seoul() {
    override fun priesident(name: String) {
        super.priesident(name)
    }

    override fun mayor(name: String) {
        super.mayor(name)
    }
    fun head(name: String) {
        println("현재 강남구청장은 $name 입니다.")
    }
}
```

상속 계층에 따라 상위 클래스의 변경이 하위 클래스에 영향을 주기 때문에, 상위 클래스의 변화가 
모든 하위 클래스에 영향을 줄 수 있다. 이는 클래스 계층도에 있는 클래스들을 한 개의 거대한 단일 구조처럼 만드는 결과를 초래한다.  
위의 코드에서 자식 클래스들은 모두 Korea 라는 부모클래스를 상속받고 있다. 만약 대통령이 바뀌게 된다면 하위 클래스들은 모두 영향을 받는다.  
만약 클래스의 계층도가 더욱 커진다면 상위 클래스를 변경하는 것은 더욱 어려워 질 것이다.

<br>
<br/>

### 클래스의 불필요한 증가
- 유사한 기능을 확장하는 과정에서 클래스의 개수가 불필요하게 증가할 수 있다.
- 코틀린이나 자바는 다중 상속을 할 수 없으므로, 상속받을 수 있는 부모클래스의 개수가 제한된다.
  - 즉 제한된 상속을 받아야 하므로, 이미 구현된 클래스들이 있음에도 불구하고, 다시 구현을 해야하는 상황이 생길 수 있다.
  - 이는 클래스의 불필요한 증가로 이어진다.

<img width="700" alt="스크린샷 2022-02-12 오후 3 08 02" src="https://user-images.githubusercontent.com/79504043/153699414-39a8f057-5028-4937-95f3-c20ae2010a7c.png">

위의 그림처럼 상위 클래스에 CompressedStorage 클래스와 EncryptedStorage 클래스가 있음에도 불구하고, 압축과 암호화를 동시에 하기 위해 다른 클래스를 만들어 주어야 한다.  
코틀린과 자바에서는 다중 상속을 할 수 없으므로 한 개의 클래스만 상속받고 다른 기능은 별도로 구현해야 한다. 필요한 기능의 조합이 증가할수록, 상속을 통한 기능 재사용은 클래스의 증가를 불러오게 된다.

<br>
<br/>

### 상속의 오용
말 그대로 상속 자체를 잘못 사용할 수 있다.  
예시를 들어보자. 컨테이너의 수화물 목록을 관리하는 클래스가 다음과 같은 세 가지 기능을 제공한다고 하자.
- 수화물을 넣는다.
- 수화물을 뺀다.
- 수화물을 넣을 수 있는지 확인한다.

이 기능을 어느 개발자가 구현한다고 했을 때, 목록 관리 기능을 직접 구현하지 않고, ArrayList 가 제공하는 기능을 상속 받아서 사용하기로 결정했다.  
구현 결과는 다음과 같다.

```kotlin
data class Luggage(val size: Int = 3)

class Container (var maxSize: Int): ArrayList<Luggage>() {
    var currentSize: Int = 0

    fun put(lug: Luggage) {
        if (!canContain(lug)) println("컨테이너가 꽉 찼습니다!")
        super.add(lug)
        currentSize += lug.size
    }

    fun extract(lug: Luggage) {
        super.remove(lug)
        this.currentSize -= lug.size
    }

    fun canContain(lug: Luggage): Boolean {
        return maxSize >= currentSize + lug.size
    }
}
```
이 Container 클래스를 사용하는 방법은 다음과 같다.

```kotlin
fun main() {
    val lugSize = Luggage(3)
    val container = Container(5)
    if (container.canContain(lugSize)) container.put(lugSize)
}
```

코드를 작성한 개발자는 이 코드에 만족하고 있었지만, 일부 개발자들은 잘못된 방법으로 이 클래스를 사용할 수 있다.  
우리가 사용하는 인텔리제이 같은 IDE 들은 코드 자동 완성 기능을 제공하는데 Container 클래스에 정의된 세 개의 메서드뿐만 아니라 상위 클래스인 ArrayList 에 등록된 메서드의 목록을 함께 보여준 것이다.  
따라서 개발자들은 코드 작성자가 만든 put 이라는 메서드와 느낌이 비슷한 add 메서드를 사용하여 비정상적인 작동을 하게 된다.  

```kotlin
    val lugSize = Luggage(3)
    val container = Container(5)
    if (container.canContain(lugSize)) container.add(lugSize) // put 이랑 비슷한 add 메서드를 사용함으로써 원하는 동작이 작동하지 않음.
```


위와 같은 문제가 발생하는 이유는 Container 는 ArrayList 가 아니기 때문이다. 상속은 IS-A 관계가 성립할 때 사용해야 한다. 같은 종류가 아닌 클래스를 상속받게 되면 잘못된 사용으로 인한 문제가 발생할 수 있다.

<br>
<br/>

## 조립을 통한 기능 재사용 

두 클래스가 IS A 관계가 아니라면, 그 두 클래스는 서로 다른 역할을 수행한다는 의미를 갖는다.
앞서 Container 는 ArrayList 처럼 클래스 자체로써 객체의 목록을 관리하는 역할을 수행하지 않는다. 단지, 짐의 목록을 관리하기 위한 목적으로 ArrayList 의 기능이 필요했던 것이다.  
이렇게 역할이 다른 객체의 기능을 재사용하고 싶다면, 조립(composition)을 통해서 재사용하는 것이 좋다.  

조립을 통한 재사용은 다음과 같이 필드로 재사용할 객체를 정의하고 메서드 내부에서 해당 필드를 사용하는 식으로 구현된다.

```kotlin
data class Luggage(val size: Int = 3)

class Container (var maxSize: Int) {
    var luggages = ArrayList<Luggage>()
    var currentSize: Int = 0

    fun put(lug: Luggage) {
        if (!canContain(lug)) println("컨테이너가 꽉 찼습니다!")
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

fun main() {
    val lugSize = Luggage(3)
    val container = Container(5)
    if (container.canContain(lugSize)) container.put(lugSize) // 조립으로 인해 ArrayList 의 add 메서드는 사용할 수 없음.
                                                              // 문제가 발생할 가능성을 애초에 제거함
}
```

<br>
<br/>

## 상속은 언제 사용할까?
상속은 재사용이라는 관점이 아닌 기능의 확장이라는 관점에서 적용해야 한다. 추가로 위에서 말한 명확한 IS-A 관계가 성립해야 한다.

먼저 기능의 확장을 생각해보겠습니다. 안드로이드에서 위젯과 관련 클래스들 정의할때, 각 클래스에 들어갈 필수적인 기능이 존재한다. TextView 에 대해 정의 하다보니, TextView 로써의 필수적인 기능엔 View 로써의 필수적인 기능(예를 들면 화면에 보여지는 기능)이 필요하다. 이번엔 Button 에 대해 정의하다보니, Button 로써의 필수적인 기능엔 TextView 로써의 필수적인 기능(예를 들면 버튼에 글씨가 보여지는 기능)이 필요하다는 것을 알게된다. 
새로운 위젯들이 기존 위젯들을 확장하여 만들어진다는 것을 알 수 있다.
- View 로써의 필수적인 기능이 있다.
  - TextView 로써의 필수적인 기능이 있다.
    - Button 로써의 필수적인 기능이 있다.

또한 이렇게 각 위젯의 필수적인 기능을 구상하면서, 기존 위젯들을 확장하여 만들어야한다는 것을 알게되면 위에서 말한 IS-A 관계도 명확해진다.
- ImageButton 은 Button 이다. 
  - Button 은 TextView 이다.
    - TextView 는 View 이다.

---