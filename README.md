# 개발자가 반드시 정복해야 할 객체 지향과 디자인 패턴

## Chapter4. 재사용: 상속보단 조립

---

### 1. 상속과 재사용

- 상속을 이용해 기존의 클래스의 기능들을 재사용해서 새로운 클래스를 만들 수 있다.
- 그러나 상속은 몇가지 단점이 존재한다.

    1. **변경의 유연함**이 불가능한 단점
        - 어떤 클래스를 상속받은 경우 그 클래스에 **의존**하게 된다.
        - 예를 들어, B클래스가 A클래스를 상속받고, C클래스는 B클래스를 상속받게 됐을 때 C클래스의 일부가 변경될 경우 B, C클래스에도 그 영향을 미치게 된다.

        ```kotlin
        open class A {}
        open class B : A() {}
        open class C : B() {}
        ```

    2. **클래스의 불필요한 증가**가 발생하는 단점
        - 클래스를 이용한 상속일 경우, 하나의 클래스만 상속받을 수 있기 때문에 기존의 기능을 유지하면서 새로운 기능을 추가하기 위해서는 새로운 클래스를 만들어야 하기 때문에 클래스가 불필요하게 증가하게 된다.

    3. **상속의 오용**이 발생하는 단점
        - 클래스를 상속받으면 상위 클래스의 기능까지 접근할 수 있게 된다. 그렇게 되면 외부에서 클래스를 사용할 때 의도하지 않은 기능을 수행할 가능성(오용)이 발생할 수 있다.

        ```kotlin
        /**
        * getValue 메소드만 이용해야 함
        */
        class CustomImutableList(private val list: ArrayList<Int>) : ArrayList<Int>() {
            fun getValue(index: Int) = list[index]
        }

        fun main() {
            val list = arrayListOf(1, 2, 3, 4, 5)
            val customList = CustomImutableList(list)
            customList.add(1)
        }
        ```

        - 위 코드는 `ArrayList`를 상속받아 `CustomImutableList` 클래스를 선언했고 값을 추가하지 못하도록 `getValue` 메소드만 구현했다고 가정한다.
        - 그러나 `main`에서는 `ArrayList`의 메소드인 `add`메소드를 호출해 값을 추가할 수 있게 되는 문제가 발생한다.
        - 분명 개발자는 getValue 메소드만 이용해야한다고 명시했지만 이 클래스를 사용하는 다른 개발자가 이렇게 오용을 할 수 있게 된다.
        - 이와같은 문제가 발생할 수 있으므로 오용의 여지가 없도록 클래스를 잘 작성해야하는데, 기본적으로 `IS-A`관계가 되도록 작성해야 한다. (고양이는 동물이다, 강아지는 동물이다 등)

---

### 조립을 이용한 재사용
- `객체 조립`은 여러 객체를 묶어 더 복잡한 기능을 제공하는 객체를 만들어내는 것으로 객체 지향 언어에서는 보통 필드를 이용에서 다른 객체를 참조하는 방식으로 구현된다.
- 조립을 잘 이용하면 **클래스의 불필요한 증가**의 단점을 개선할 수 있게 된다.

    ```kotlin
    class Encryptor {...}
    class Compressor {...}

    class FlowController {
        private var encryptor: Encryptor? = null
        private var compressor: Compressor? = null

        fun setEncryptor(encryptor: Encryptor) {
            this.encryptor = encryptor
        }

        fun setCompressor(compressor: Compressor) {
            this.compressor = compressor
        }

        fun process() {
            ...
            if (compressor != null) data = compressor.compress(data)
            ...
            if (encryptor != null) data = encryptor.encrypt(data)
            ...
            return data
        }
    }
    ```
    
    - 위 코드와 같이 객체를 조립하게 되면 위에서 언급한대로 set을 이용해 정의되어 있을 때에만 해당 기능을 수행하므로 기능 추가를 위해 새로운 클래스를 만들 필요가 없다.
    
    - 또한, 위와 같이 객체를 조립해 구현하게 되면 런타임에 객체를 교체해 다른 기능을 수행할 수 있도록 할 수도 있다는 장점이 존재한다.

    - 구현과 구조가 복잡해지는 단점이 있지만 변경의 유연함을 확보한 것에서 오는 장점이 더 크기 때문에 기능을 재사용해야 할 경우 상속보다는 조립을 먼저 고려해야 한다.

---

### 상속은 언제 사용해야 할까
- 위에서 언급한 것만 보면 상속은 사용하면 안된다고 판단할 수도 있지만, `상속은 재사용이라는 관점보다는 기능의 확장이라는 관점에서 상속을 적용`해야 한다.

- 그 예로, 안드로이드의 UI 위젯이 있다.

![안드로이드 상속](https://weibeld.net/android/assets/view-hierarchy.png)
