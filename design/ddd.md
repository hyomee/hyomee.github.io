# 도메인주도설계

도메인 주도 개발은 Eric Evans가 2003년 출간한 Domain-Driven Design책으로 처음 소개한 방법론으로 “<mark style="color:purple;">**유용한 소프트웨어를 개발하고 싶다면 도메인 귀를 기울여라**</mark>“ 라는 슬로건으로 시작된다.

## 1. 도메인이란

일반적인 요구사항, 전문 용어, 그리고 컴퓨터 프로그래밍 분야에서 문제를 풀기 위해 설계된 어떤 소프트웨어 프로그램에 대한 기능성을 정의하는 연구의 한 영역이다,

* 소프트웨어로 해결하고자 하는 문제 영역
  * 통신회사의 청약과 관련된 지식 = 통신도메인
  * 은행 업무에 대한 지식 = 은행 도메인

## 2.  도메인 모델

* 특정 도메인을 개념적으로 표현한 것
* 도메인 모델을 사용하면 여러 관계자들(개발자, 기획자, 사용자 등)이 동일한 모습으로 도메인을 이해하고 도메인 지식을 공유하는 데 도움이 된다
* 모델의 각 구성 요소는 특정 도메인을 한정할 때 비로소 의미가 완전해지기 때 문에, 각 하위 도메인마다 별도로 모델을 만들어야 한다.
* 애자일 개발 방식으로 반복 수행 하여 완성도를 높일수 있다.
* 예) 통신회사에 가입 처리시 : 고객 모델, 가입의 상태 관리를 하는 가입모델, 구매한 상품 모델

## 3. 도메인 주도 개발이란&#x20;

* 도메인 전문가와 개발자 사이의 의사소통의 어려움은 해소 하기 위해 보편언어 (Ubiquitous Languages)를 사용여 도메인과 구현을 충분히 만족 하는 모델을 만드는 것
* 설계와 구현은 계속된 수정 과정을 거친다.
* 도메인에 대한 역할과 책임을 부여 하기 위해서 경계를 설정 한다. ( Bounded Context )

## 4. Bounded Context

* 독립적으로 서비스 될 떼 문제없는 업무 범위
* 역할과 책임 명확
* 도메인 : Bounded Context는 1:1이 이상적이다.
* 다른 Context 연결은 API통신으로만 한다.
* 주의할 점 : 하위 도메인 모델이 뒤섞이지 않도록 한다.
  * &#x20;도메인이 섞이면 기능 확장이 어렵게 되고 서비스 경쟁력이 떨어진다.
  * 개별 Bounded Context Package로 구성한다.

## 5. 도메인 주도 설계 기본 요소

### 5-1. Entity, Value

#### 5-1-1. Entity

* 속성이 아닌 식별성을 기준으로 정의되는 도메인 객체&#x20;
* 식별자는 Entity 객체마다 고유해서 각 Entity는 서로 다른 식별자를 갖는다.&#x20;
* 생성 시점에 필요한 것을 전달 한다.&#x20;
* &#x20;setXXX Method는 완전한 상태가 아닐 수 있으므로 사용 자제한다

#### 5-1-2. Value

* 식별상이 아닌 속성을 이용해 정의 되는 불변 객체&#x20;
* 의미를 명확하게 표현 하거나 두 개 이상의 데이터가 개념적으로 하나인 경우 사용&#x20;
* 값의 변경은 새로운 Value 객체를 할당&#x20;
  * &#x20;주민번호는 String로만 표현 되지 않는다. 즉 자료형 + 검증 로직이 포함 된다.

<figure><img src="../.gitbook/assets/image (223).png" alt=""><figcaption><p>Entity와 VO</p></figcaption></figure>

### 5-2. Aggregate

* 관련 객체( 큰  단위) 하나로 묶음으로 데이터를 변경 하는 단위
* 루트 엔티티를 갖는다.&#x20;
  * 애그리커트가 제공해야 할 도메인 기능 구현한다.&#x20;
  * 내부 구현을 숨겨서 애그리거트 단위로 캡슐화한다&#x20;
  * 애그리커드에 속해 있는 Entity와 Value를 이용하여 기능 구현한다.&#x20;
  * 애그리커트의 참조는 id를 이용한 참조 방식 사용한다.&#x20;
  * 직접 참조 시 편리하지만 성능 및 확장 어려움 발생힌다.
  * 외부에서 객체 참조 시 루트 애그리커트를 통해서 참조한다.
* Aggregate에 속해 객체는 유사 하거나 동일한 라이프 사이클을 갖는다.
* 한 애그리거트에 속한 객체는 다른 애그리거트에 속하지 않는다.
* 자기자신을 관리 할 뿐 다른 애그리커트를 관리 하지 않는다.

<figure><img src="../.gitbook/assets/image (224).png" alt=""><figcaption><p>Aggregate 관계</p></figcaption></figure>

### 5-3. Service

* 업무의 흐름 : Use Case를 생각 해라
* 도메인 로직을 담당한다.
* 상태 없이 로직만 구현한다.
  * 연산에 영향을 주는 상태를 잦기 않는다.
* <mark style="color:purple;">**Transaction의 주체이다**</mark>
* Domain Object애 위치 시키기 어려운 Operation으로 Domain 객체 호출한다.
  * 가입 상태를 변경 하고 상품을 등록 한다면 가입상태변경 애그리거트와 상품 등록 애그리거트 로 분리 하고 서비스에서 로직 구현한다.
* Module(Package)&#x20;
  * 인지 과부하 방지와 낮은 결합도와 높은 응집도를 가진다.

<figure><img src="../.gitbook/assets/image (225).png" alt=""><figcaption><p>서비스 예시</p></figcaption></figure>

### 5-4. Repository

* 구현을 위한 모델이다.
* 애그리커드 단위로 도메인 객체를 저장하고 조회 하는 기능&#x20;
  * &#x20;애그리커드에 대한 영속성 관리를 통한 일관성 유지
* 도메인 영역과 데이터 인프라스럭쳐 계층 분리하여 데이터 계층에 대한 결합도 낮추기 위한 방안이다

### 5-5. Factory

1. 어떤 객체나 전체 Aggregate를 생성하는 일이 복잡하다면 팩토리를 이용해 이것 을 캡슐화 할 수 있다.
