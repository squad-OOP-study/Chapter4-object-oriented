# 객체지향과 디자인 패턴 : chap4 재사용 : 상속보단 조립

- 2022.2.13

> 배운점
- 조립을 이용했을 때 런타임시 객체 교체 가능
- 상속을 구현할 때 사용자 입장에서 클래스의 메서드 사용시 오해가 발생할 여지가 있는지 살펴봐야 하겠다.

> 학습목표
- 상속을 통한 재사용 과정에서 발생할 수 있는 문제점 살펴보기 
- 다른 재사용 방법인 객체 조립을 통해 위 문제점 해소 방안 살펴보기
___

> 상속을 통한 재사용의 문제점

1. 상위 클래스 변경이 어려움
    - 변경의 유연함 측면에서 단점
    - **클래스를 상속받는 것은 그 클래스에 의존하는 것.**
    - 상위 클래스의 변경이 계층도를 따라 하위 클래스에 전파
2. 클래스의 불필요한 증가
    - 유사한 기능 확장하는 과정에서 불필요하게 클래스 증가 가능
    - 예시) 압축 클래스, 암호화 클래스 가 있을 때  
    압축과 암호화를 동시에 하기 위해 2가지 방법을 사용할 수 있다.
      - 두 기능 모두 제공하는 새로운 클래스 생성
      - 한 개의 클래스만 상속받고 다른 기능은 별도로 구현
3. 상속의 오용
    - 예시) 컨테이너의 수화물 목록 관리 클래스 생성 : Container Class
    - 필요 기능 : 수화물 넣기/수화물 빼기/수화물 넣을 수 있는지 확인
    - 목록관리 기능 : ArrayList 상속받아 사용하기로 결정
        - Container 클래스에 정의 된 3개의 메서드 뿐아니라 ArrayList의 메서드도 사용할 수 있게 된다.
        - 해당 클래스 사용자 입장에서는 도메인대로 올바르게 작동하는 메서드 구별이 모호해 클래스 사용을 오용하게 된다.
      - 상속을 오용한 전형적인 예   
      
    <br>

    - 해당 문제가 발생한 이유
        - 상속은 IS-A 관계가 성립할 때만 사용해야 한다.   
          - Container는 ArrayList가 아니기 때문이다.          
        - 상속관계의 클래스가 서로 다른 책임을 갖고 있다. 
          - Cotainer는 수화물을 보관하는 책임을 갖는다.
          - 그러나 ArrayList는 목록을 관리하는 책임을 갖는다.

    - [예시 코드]
    ```java
    public class Container extends ArrayList<Luggage> {
        private int maxSize;
        private int currentSize;

        public Container(int maxSize) {
            this.maxSize = maxSize;
        }

        public void put(Luggage lug) throws NotEnoughSpaceException {
            if (!canContain(lug))
                throw new NotEnoughSpaceException();
            super.add(lug);
            currentSize += lug.size();
        }

        public void extract(Luggage lug) {
            super.remove(lug);
            this.currentSize -= lug.size();
        }

        public boolean canContain(Luggage lug) {
            return maxSize >= currentSize + lug.size();
        }
    }
    ```

    - 잘못된 클래스 사용 발생
    ```java
    if(c.canCotain(size3Lug)) {
        c.put(size3Lug); //Container 여분 5에서 2로 줄어듬
    }

    if(c.canCotain(size2Lug)) {
        c.add(size2Lug); //Container 여분 2에서 줄지 않음
    }
    if(c.canCotain(size2Lug)) {
        c.add(size2Lug); //여분이 없으므로 통과되면 안되지만, 통과됨. 
    }
    ```

> 객체 조립(coposition) 이란
- 여러 객체를 묶어서 더 복잡한 기능을 제공하는 객체를 만들어내는 것
- 객체 지향 언어에서 보통 다른 객체를 참조하는 방식으로 구현
- 한 객체가 다른 객체를 조립해서 필드로 갖는다는 것은 다른 객체의 기능을 사용한다는 의미
    ```java
        private Encryptor encryptor = new Encrytor();
    ```

> 조립을 이용한 재사용 장점 
1. 클래스 증식 문제 해결
   - 상속을 이용한 재사용 UML
      ![상속uml](https://user-images.githubusercontent.com/55780251/153736805-2d6ef033-540b-46b1-a4f0-92208d1d8211.jpg)

   - 조립을 이용한 재사용 UML     
      ![조립uml](https://user-images.githubusercontent.com/55780251/153736809-d09a4b8d-151a-4992-a73c-cde322d7eaf0.jpg)

2. 클래스 오용 문제 해결 
3. 런타임에 조립 대상 객체 교체 
   - [X] 런타임에 교체가 가능하다
   - Storage 클래스는 setCompressor()를 통해 사용할 Compreesor객체 전달 받을 수 있는데 아래 코드처럼 런타임에 사용할 Compressor객체를 바꿀 수 있다.
    ```java
    Stroage storage = new Storage();
    storage.save(someFileData); //Compressor 객체로 압축

    storage.setCompressor(new FastCompressor()); // 사용할 객체 교체
    storage.save(anyFileData); //FastCompressor객체로 압축
    ```


> 상속보다는 객체 조립을 사용
- 객체 조립을 모든 상황에서 사용해야 한다는 것은 아니다.
- 상속은 변경에서 유연함이 떨어질 가능성이 높으니 객체 조립을 먼저 고민.

> 상속에 비해 **조립을 통한 재사용의 단점**
-  상대적으로 런타임 구조가 복잡
- 상속보다 구현이 더 어려움

> 조립 방식을 이용한 위임(delegation)
- 할일을 다른 객체에게 넘기는 것.
- 위임은 보통 객체 조립 방식을 이용해서 구현.
- 위임의 구현
  - 1 요청을 위임할 객체를 필드로 연결
  - 2 객체를 새로 생성해서 요청을 전달

- 조립 방식과 객체 생성 방식의 위임 예시 코드
  ```java
  - 조립형태로 위임  
  public abstract class Figure {
    private Bountds bounds  = new Bounds(); //조립 형태로 위임
    
    public boolean contains(Point point) {
        return bounds.cotains(point.getX(), point.getY());
    }
  }
  

  - 객체를 새로 생성해서 위임
  public abstract class Figure {
    pulbic boolean contains(Point point) {
        Bounds bounds = new Bounds(x, y, width, height); // 객체 생성해서 위임
        return bounnds.coutains(point.getX(), point.getY());
    }
  }
  ```

- 다른 객체에게 위임함으로 메서드 호출이 추가되기 때문에 실행 시간은 다소 증가한다. 
- 연산 속도가 매우 중요한 시스템에서는 많은 위임코드가 성능에 문제 일으킬 수 있지만
- 대부분은 위임을 통해 유연함, 재사용의 장점이 크다.

> 상속은 언제 사용하나?
- 재사용의 관점이 아닌 **기능의 확장**이라는 관점에서 상속 적용
- **IS-A 관계가 명확하게 성립**되어야 한다.
- 그러나 최초에는 명확한 IS-A 관계로 보여서 상속을 이용하더라도 **이후에 클래스 개수가 불필요하게 증가하는 문제가 발생하거나 상위 클래스의 변경이 어려워지는 등 상속의 단점이 발생**하면 **조립으로 전환하는 것을 고려**해야 한다.


___

> 그룹 스터디를 통해 새롭게 배우고 깨달은 점
- 스타크 : 내용은 우리가 항상 사용하던 내용이었지만 책에서 장단점을 보고 나니 상속, 조립할 때 생각하면서 사용할 것 같아서 많은 도움이 되었다.
- Jay : 책 이외에 추가적인 상속과 조립의 선택 기준. 메모리!
    - 메모리 객체 생성 비용을 줄여줄 수 있는 방법에 Value class 라는 것이 있다.  - 참고 url : https://velog.io/@dhwlddjgmanf/Kotlin-1.5에-추가된-value-class에-대해-알아보자
    - 라이너스 :  비교적 최신 언어 코틀린은 위임을 by를 통해 구현할 수 있는 방법이 있다. lazy를 통해 위임을 늦추거나 할 수 있다. "코틀린 by 위임"
- Han : 필드로 조립하는 것도 객체 생성 방법의 변경으로 인한 의존이 생길 수 있다. 하지만 의존성 역전에 의해 해당 부분은 처리 될 수 있을 것 같다. 
- 히데 : 조립이 구현이 상속에 비해 어려운 점은 코드로 보지 않아 와닿지 않는다.
- 스티치 : 키워드 "일급컬렉션",컬렉션을 객체 안에서 사용할 수 있게 한다. 자세한 것은  클린코드TDD참고. 


