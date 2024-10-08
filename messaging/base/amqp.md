# AMQP

AMQP(Advanced Message Queuing Protocol)는 메시지 지향 미들웨어를 위한 개방형 표준 응용 계층 프로토콜입니다. 이 프로토콜은 메시지 지향, 큐잉, 라우팅(P2P 및 발행-구독), 신뢰성, 보안 기능을 제공하며 대표적인 소프트웨어로 RabbitMQ, SwiftMQ 등이 있습니다.

**AMQP스펙**은 네트워크 프로코콜의 정의와 서비스의 동작 방식을 정의하고 있습니다. 여기서 서비스의 동작방식을 **AMQ 모델**이라 합니다.

## 1. **AMQ 모델**

**AMQ (**Advanced Message Queuing) **모델**은 메시지 라우팅 동작을 정의하는 메세지 브로커의 새가지 추상 컴포넌트를 다음과 같습니다.

<figure><img src="../../.gitbook/assets/image (480).png" alt="" width="563"><figcaption><p>AMQ Model</p></figcaption></figure>

* **익스체인지(Exchange)**: 클라이언트에서 받은 메세지를 큐로 전달하는 컴포넌트
* **큐(Queue)**: 메시지를 저장하는 디스크상의 자료 구조
* **바인딩(Binding)**: 익스체인지에서 전달된 메세지를 어떤 큐에 저장해야 하는지 정의하는 컴포넌트

## 2. **AMQP**

<figure><img src="../../.gitbook/assets/image (476).png" alt=""><figcaption><p>AMQP 개념</p></figcaption></figure>

생산자/게시자(Publish/Producer)는 메시지를 만들어 브로커에보내고  브로커는  컨슈머에게 순차적으로 전달합니다. 여기서 보로커는 메세지를 하나 이상의 큐로 라우팅하는 역할을 하는 Exchange(교환기) 를 가지고 있습니다. AMQP는생산자, 메시지 브로커 및 소비자 간의 상호 운용성을 만들어&#x20;

* 신뢰할 수 있고
* 전달이 보장 되며
* 순서대로 메세지를 전달하는&#x20;

기능을 재공합니다.

## 3. 메세지 교환 방식

AMQP 모델에서 메세지 교환은 다음 방법으로 진행 합니다.

* **직접 교환(Direct)**: Key를 사용한 라우팅 방식으로 메시지는 메시지의 라우팅 키와 동일한 이름의 큐로 전달하는 방식입니다.
* **팬 아웃(Fan-out)**: 라우팅 키를 무시하고 메시지는  교환 가능한 범위내에서 모든 큐에 메시지를 전달하는 방식으로 브로드캐스트 형태로 라우팅하기에 적합합니다.
* **토픽(Topic)**: 라우팅 패턴을 사용하여 메세지를 일부 연결된 큐로 메세지를 전달하는 방식으로 게시/구도 유형이라고 합니다. 토픽 교환은 일반적으로 메세지의 멀티캐스트 라우팅에 사용됩니다.

<details>

<summary>브로드캐스트 </summary>

동일한 이더넷에 접속해 있는 컴퓨터 전체에게 데이터를 보내는 통신 방식입니다. 이는 일대다 통신으로, 로컬 랜 상에 연결된 모든 네트워크 장비들에게 메시지를 전송하는 것을 의미로브로드캐스트는 주로 다음과 같은 상황에서 사용됩니다:

1. **ARP (Address Resolution Protocol)**: 네트워크 상에서 IP 주소를 MAC 주소로 매핑하는 프로토콜에서 브로드캐스트를 사용합니다. ARP 요청은 로컬 네트워크의 모든 시스템에게 전송되어 해당 IP 주소에 대한 MAC 주소를 찾습니다.
2. **DHCP (Dynamic Host Configuration Protocol)**: IP 주소를 동적으로 할당하는 DHCP 서버가 클라이언트에게 IP 주소를 제공할 때 브로드캐스트를 사용합니다. DHCP 클라이언트는 네트워크 상의 모든 시스템에게 IP 주소를 요청합니다.
3. **Wake-On-LAN (WoL)**: 원격으로 컴퓨터를 켜거나 절전 모드에서 깨우는 기능에서도 브로드캐스트를 활용합니다. 이 경우 디렉티드 브로드캐스트 주소를 사용하여 패킷을 전송합니다[3](https://m.blog.naver.com/wnrjsxo/221250742423).

</details>

<details>

<summary>멀티캐스트:</summary>

한 번의 송신으로 메시지나 정보를 목표한 여러 컴퓨터에 동시에 전송하는 것을 말합니다

</details>



참고: [위키백과\_AMQP](https://ko.wikipedia.org/wiki/AMQP)
