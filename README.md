# CH4. 재사용 상속보단 조립
> 학습목표
- 상속을 통한 재사용 과정에서 발생할 수 있는 문제점 살펴보기
- 다른 재사용 방법인 객체 조립을 통해 위 문제점 해소 방안 살펴보기
- 언제 상속을 사용해야하는지 살펴보기

#### 상속의 정의
- 상속(inheritance)이란 기존의 클래스에 기능을 추가하거나 재정의 하여 새로운 클래스를 정의하는 것을 의미
- 이러한 상속은 캡슐화, 추상화와 더불어 객체 지향 프로그래밍을 구성하는 중요한 특징이다. 
- 상속을 이용하면 기존에 정의되어 있는 클래스의 모든 필드와 메소드를 물려받아, 새로운 클래스를 생성할 수 있다. 이때 기존에 정의되어 있던 클래스를 부모 클래스(parent class) 또는 상위 클래스(super class), 기초 클래스(base class)라고도 한다. 그리고 상속을 통해 새롭게 작성되는 클래스를 자식 클래스(child class) 또는 하위 클래스(sub class), 파생 클래스(derived class)라고 부른다.

## [1]. 상속과 재사용
### 상속을 통한 재사용의 문제점
#### 1. 상위 클래스 변경이 어려움
- 클래스를 상속받는 것은 그 클래스에 의존하는 것. 의존한다는 것은 의존하는 대상이 변경되면 영향을 받는다는 의미이다.
- 상위 클래스의 변경이 계층도를 따라 하위 클래스에 전파
- 이러한 영향으로 인해, 상속 구현으로 된 구조에선 (상속을 통한 재사용 구조에선), 상위 클래스의 변경이 어렵게 된다. (변경의 유연함 측면에서 단점)  
<예시>
```
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
- 위의 코드에서 자식 클래스들은 모두 Korea 라는 부모클래스를 상속받고 있다. 만약 대통령이 바뀌게 된다면, 즉 Korea 클래스에 변경이 발생한다면 하위 클래스들은 모두 영향을 받는다.

#### 2. 클래스의 불필요한 증가
- 유사한 기능 확장하는 과정에서 불필요하게 클래스 증가 가능
- 코틀린이나 자바는 다중 상속을 할 수 없으므로, 상속받을 수 있는 부모클래스의 개수가 제한된다. 
  - 즉 제한된 상속을 받아야 하므로, 이미 구현된 클래스들이 있음에도 불구하고, 다시 구현을 해야하는 상황이 생길 수 있다. 이는 클래스의 불필요한 증가로 이어진다.
#### 3. 상속의 오용
- 상속 자체를 잘못 사용할 수 있음을 의미한다.
  - 클래스를 상속받으면 상위 클래스의 기능까지 접근할 수 있게 된다. 그렇게 되면 외부에서 클래스를 사용할 때 의도하지 않은 기능을 수행할 가능성(오용)이 발생할 수 있다.
    ![2](https://user-images.githubusercontent.com/95393311/153737675-83b63134-3ae8-4872-9a6a-730caa5bd550.JPG)  
      <이클립스에서 볼 수 있는 자동 완성 기능. 해당 기능에서는 add와 put 모두 표시하고 있다. 이로 인해 의도치 않게 하위 클래스의 put이 아닌 상위 클래스의 add가 오용될 가능성이 있다>  
<책에서 나온 예제>
```
class Container(private val maxSize: Int): ArrayList<Luggage>() {
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
    if(container.canContain(luggage)) container.put(luggage)
    // if (container.canContain(luggage)) container.add(lugSize) put 이랑 비슷한 add 메서드를 사용함으로써 원하는 동작이 작동하지 않음.

}
```
- 상속 클래스 개발자는 container에 put을 사용하도록 만들었지만, 잘못된 방법으로 add을 사용해 상속의 잘못된 방법으로 활용될 수 있다.  

- 해당 문제(상속의 오용)가 발생한 이유 
  - 상속은 IS-A 관계가 명확히 성립할 때, 사용해야 한다. (Container는 ArrayList가 아니다) (예시: 고양이는 동물이다, 강아지는 동물이다 등)
  - 상속 관계의 클래스가 서로 다른 책임을 갖고 있다. 
    - Container는 수화물을 보관하는 책임을 갖는다. 
    - 그러나 ArrayList는 목록을 관리하는 책임을 갖는다.


## [2]. 조립을 이용한 재사용
- 객체 조립(composition) 은 여러 객체를 묶어서 더 복잡한 기능을 제공하는 객체를 만들어내는 것
- OOP에서 객체 조립은 필드에서 다른 객체를 참조하는 방식을 의미한다.
  - 한 객체가 다른 객체를 조립해서 필드로 갖는다는 것은 다른 객체의 기능을 사용한다는 의미  
  `val encryptor = new Encrytor()` 

#### 조립을 이용한 재사용의 장점
- 조립을 이용해 재사용성을 구현할 경우, 상속을 통한 재사용성 구현에서 발생할 수 있는 문제점의 많은 부분들을 해결할 수 있다.
- 두 객체(클래스)간의 의존 관계가 사라지게 되기 때문에, 변경의 어려움이 발생하지 않는다.
- 또한, 필요한 기능이 추가될 경우 해당 기능을 가지고 있는 객체를 필드에 추가하면 되기 때문에 추가적으로 클래스를 구현할 필요가 없어진다.
- 상속의 오용으로 인한 문제 또한 해결된다. 각 기능이 독립적으로 구현되어 있으며, 한 객체가 그것을 가져다 쓰는 것이기 때문에 IS-A 관계를 가질 필요가 없어진다.
- 상속의 문제점을 해결하는 장점뿐만이 아니라 조립 자체만의 장점도 존재한다. 
  - 런타임에 조립 대상을 교체할 수 있다. 
  - 상속의 경우, 대상을 교체하기 위해서는 소스 코드를 수정하고 재컴파일 할 필요가 있다.

<앞서 나온 예제를 조립 방식으로 다시 구현>
```
class Container (private val maxSize: Int) {
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
fun main() {
    val lugSize = Luggage(3)
    val container = Container(5)
    if (container.canContain(lugSize)) container.put(luggage) // 조립으로 인해 ArrayList 의 add 메서드는 사용할 수 없음.
                                                              // 따라서, 오용 문제가 발생할 가능성을 애초에 제거함
}
```

#### 조립을 이용한 재사용성의 단점
- 상대적으로 런타임 구조가 복잡
- 상속보다 구현이 더 어려움

### 위임
```
public class MyController {

    MyRepository myRepository; // has a 관계

    public void save() {
        myRepository.save();   // myRepository에게 save 권한을 위임에서 처리
    }
}
```
- 위임: 내가 할일 다른 객체에게 넘긴다.
- 일반적으로 조립 방식을 이용해 구현한다
- 위임의 구현은 조립처럼 위임할 객체를 필드로 연결할 수 있다.
- but 다른 객체에게 책임을 넘긴다는 의미가 있음으로, 객체를 생성해서 요청을 전달해도 상관없다.
- 위임과 조립 모두 객체지향적 구현과정에서 발생하는 세분화되고 많은 양의 객체들을 처리하기 위한 바람직한 처리 방법들이다.
- 위임의 경우 내가 바로 실행 가능한 것을 다른 객체에게 요청하게 됨으로, 메소드 호출이 발생하여 실행 시간이 증가하는 단점이 있다.
- 조립과 마찬기지로, 실행 시간 지연으로 인한 단점보다 위임으로 얻는 구조의 유연함의 장점이 더 크다.
- 코틀린에서는 `by` 키워드를 통해 위임을 구현할 수 있다.

### 상속은 언제 사용할까?
![6](https://user-images.githubusercontent.com/95393311/153738259-74767df4-f49e-461f-b44f-e19e038b6bf9.png)
   <상속의 좋은 예: 안드로이드 UI 위젯>
- 재사용의 관점이 아닌 기능의 확장이라는 관점에서 상속 적용 
- IS-A 관계가 명확하게 성립되어야 한다. 
- 그러나 최초에는 명확한 IS-A 관계로 보여서 상속을 이용하더라도 이후에 클래스 개수가 불필요하게 증가하는 문제가 발생하거나 상위 클래스의 변경이 어려워지는 등 상속의 단점이 발생하면 조립으로 전환하는 것을 고려해야 한다.

> 결론
>> 구현/구조의 복잡함보다 변경의 유연성에 오는 장점이 일반적으로 크기 때문에 재사용성을 구현할 필요가 있을 때, 상속보다는 객체 조립을 먼저 고려할 필요가 있다.

## 추가적인 상속과 컴포지션의 선택 기준
### 기준1
has-a / is -a 일반적으로 컴포지션 과 상속을 결정하는 가장 보편적인 기준으로서 두 클래스가 has-a 관계일때는 컴포지션으로 반대로 is - a 관계일때는 상속으로 구현합니다.
### 기준2 : 기계적(메모리) 이유
아래 두가지 방법으로 box 생성시 두 방법에 대한 메모리 블럭을 그려보면 첫번째 box 변수는 힙 안에 [10,20,30]을 가리키고 있습니다. 그리고 두번째 box 변수는 [rectangle의 주소값 , 30]을 들고 있고 rectangle 의 주소값은 다시 [10,20]을 가리키고 있을것입니다. 이에 기반해서 생각해보면 상속형 변수는 메모리에서 데이터를 한번에 가져올 수 있지만 컴포지션형 변수는 두번가져오게 되어 성능상 불이익이 발생할 수 있다.
```
// 상속
Box box = new Box(10,20,30)

// 컴포지션
Rectanle rectangle = new Ranctangle(10,20)
Box box = new Box(rectangle , 30)
```
### 기준3 : 용도의 차이
다형성을 필히 사용해야 하는 경우 상속을 선택해야합니다.


## 추가적으로 생각해볼만 한 것
1. 조립을 활용할 때, 의존성 주입을 고려할 수 있을까? (챕터 6에서 공부할 예정)
2. 일급 컬렉션이란? -> [참고자료](https://jojoldu.tistory.com/412)
3. 코틀린에서 `by` 키워드를 통한 위임 -> [참고자료](https://thinking-face.tistory.com/entry/%EC%9C%84%EC%9E%84)
4. 객체를 생성하는 비용을 줄이는 방법으로 `value class`를 활용하는 방법이 있다 -> [참고자료](https://velog.io/@dhwlddjgmanf/Kotlin-1.5%EC%97%90-%EC%B6%94%EA%B0%80%EB%90%9C-value-class%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)