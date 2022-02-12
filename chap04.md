# 🙋‍♂ 재사용: 상속보단 조립!

## 👉 상속과 재사용
- 상속은 다른 클래스의 기능을 재사용하면서 기능을 확장하는데 유용
- 하지만 변경의 유연함이 떨어짐
### 📗 상속의 단점
#### 1. 🔖 상위 클래스 변경의 어려움
![KakaoTalk_20220212_224310105](https://user-images.githubusercontent.com/95393311/153713893-bda95504-5adf-4a68-8af4-ef8a7fa054a4.jpg)
- 만약 super class를 변경한다면 super class를 상속받고 있는 모든 subclass도 영향을 받게 된다
#### 2. 🔖 클래스의 불필요한 증가
![캡처](https://user-images.githubusercontent.com/95393311/153714644-169015cd-cbde-41e5-93df-8c1fcc810dd1.JPG)
- 추가 요구 사항이 발생하여 유사한 기능을 확장하는 과정에서 불필요한 클래스가 늘어날 수 있다.
  - 예를 들어, 기존의 2가지의 클래스가 가진 기능을 합쳐야 하는 경우, 다중 상속이 불가하기 때문에 1개의 클래스를 상속받은 후에 기존의 다른 클래스의 기능을 다시 추가해야한다.
- 조립할 경우에는 간단히 2개의 클래스를 합치기만 하면 된다.
#### 3. 🔖 상속의 오용
- subclass를 이용할 때, 이클립스, 인텔리 J와 같이 IDE의 자동 완성 기능 등 어떠한 이유로 개발자가 subclass의 기능이 아니라 superclass의 기능으로 잘못 사용할 수 있다.
  ![2](https://user-images.githubusercontent.com/95393311/153715665-4d8a6a35-01d9-4925-a00a-4eee4bfb4da6.JPG)
```
open class ItemList {
    val itemList = arrayListOf<String>()

    fun add(item:String) {
        itemList.add(item)
    }
}

open class ExpiredItemList: ItemList() {
    val expiredItemList = arrayListOf<String>()

    fun put(expiredItem:String) {
        expiredItemList.add(expiredItem)
    }
}

fun main() {
    val expiredItemList = ExpiredItemList()
    expiredItemList.add("milk")
}
```
- 위의 코드에서 물품 목록 `ItemList` 클래스를 상속받은 만료된 물품 목록 `ExpiredItemList`의 기능을 이용할 때, `put()` 메서드를 이용해야 하지만 부모 클래스의 `add()` 메서드를 오용하여 의도치 않은 결과를 발생시켰다.
- 이처럼 상속 클래스를 작성할 때, 오용의 여지를 줄 수 있는 잘못을 저지를 수 있다.
- 비록 위의 예제는 IS-A 관계가 아닌 예시가 아니지만, 일반적으로 상속의 오용 문제는 `IS-A 관계`가 아닌 즉 서로 다른 책임을 가진 클래스를 상속을 통해 구현할 때 발생하기 쉽다 

### 📗 조립을 이용한 재사용
- 객체 조립(composition)
  - 여러 객체를 묶어서 더 복잡한 기능을 제공하는 객체를 만들어내는 것
  - 조립을 구현하는 방법
    - 일반적으로 필드에서 다른 객체를 참조하는 방식으로 구현
    ```
    class Test {
        val 조립하고싶은객체 = 조립하고싶은객체를참조()
    }
    ```
    - 이를 통해 Test 클래스에서 참조한(조립한)객체의 기능을 사용할 수 있다.

#### 🔖 조립의 장점
- 앞서 말한 상속의 단점을 모두 극복할 수 있다.
  ![3](https://user-images.githubusercontent.com/95393311/153716850-ecea10d2-db86-429c-9b77-7fdd3714a044.JPG)
  - 조립을 통해 불필요한 클래스가 생성되는 것을 막고 추가 요구 사항이 발생하더라도 추가 클래스와 저장 클래스에서 조립하는 코드만 만들면 된다.
- 런타임에도 언제든지 조립 대상 객체를 교체할 수 있다.

#### 🔖 조립의 단점
- 조립을 사용할 경우, 런타임의 객체 구조는 상속보다 복잡해질 수 있다.

> 결론  
>> 상속보다는 조립을 먼저 고민해보자!

#### 📗 위임(delegation)
- 내가 할 일을 다른 객체에게 넘긴다는 의미
- 책에서는 상속을 통해 위임을 구현할 수 있다고 말함
- 코틀린에서는 `by` 키워드를 통해 구현
- 위임을 사용하면 실행 시간이 다소 증가하지만 위임을 통해서 얻을 수 있는 유연함/재사용의 장점이 더 크다.

#### 📗 상속은 언제 사용하나?
1. 상속은 재사용의 관점보다는 기능의 확장의 관점에서 사용해야한다. (즉, 기능의 재사용을 위해 상속을 활용하기 보다는 기능을 확장할 때 상속을 활용하자)
2. 명확하게 IS-A 관계가 성립될 때 활용하자.
   ![5](https://user-images.githubusercontent.com/95393311/153717446-9fc21be9-1338-46a8-adaf-9f832f1d8c75.jpg)
   (대표적으로 UI 위젯이 상속의 좋은 예)
- 결론적으로 IS-A 관계가 성립하면서 점진적으로 상위 클래스의 기능을 확장할 때 사용할 수 있다. 단, 최초에는 상속을 사용했지만 기능을 확장할수록 불필요한 클래스가 많아지고 상위 클래스를 변경해야 한다면 조립을 고려해야한다.

