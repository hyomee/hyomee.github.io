---
description: 'Exchange Type: Direct'
---

# Exchange-Direct

메시지에 포함된 라우팅 키(routing key)를 기반으로 Queue로 메시지를 전달합니다. 각 Queue는 특정 라우팅 키와 연결됩니다.



아래 이미지와 같은 서비스 코드를 작성 하면서 Binding에 대한 이해를 하고자 합니다.

<figure><img src="../../../.gitbook/assets/image (500).png" alt=""><figcaption></figcaption></figure>

## 1. Exchange&#x20;

### 1-1. Exchange 설정  방법 (관리자 UI)

<figure><img src="../../../.gitbook/assets/image (489).png" alt=""><figcaption></figcaption></figure>

* 관리자 UI / Exchanges / Add a new exchange 버튼을 클릭 하고 이름, type 등을 선택 한 후 "Add exchange"버튼을 클릭하여 생성합니다.

### 1-2.  Exchange 설정  방법 (프로그램 방식)

Exchange를 만드는 동안 Name, Durable, Auto-delete 및 Exchange 유형의 3가지 속성을 처리해야 합니다. 기본적으로 생성된 교환은 지속적이며 자동 삭제는 false로 **channel.exchangeDeclare** 메서드를 사용해서 만듭니다. 다음은 기본적인 파라메터 입니다.

* exchange (교환기 이름): 교환기의 이름을 지정합니다. 이 이름은 메시지를 어떤 큐로 라우팅할지 결정하는 데 사용됩니다.&#x20;
* type (교환기 유형): 교환기의 유형을 지정합니다. 일반적으로 다음과 같은 유형이 있습니다:&#x20;
  * fanout: 모든 바인딩된 큐에 메시지를 브로드캐스트합니다.&#x20;
  * direct: 라우팅 키와 일치하는 큐로 메시지를 라우팅합니다.&#x20;
  * topic: 라우팅 패턴을 사용하여 메시지를 큐로 라우팅합니다.&#x20;
  * headers: 헤더 속성을 기반으로 메시지를 큐로 라우팅합니다.&#x20;
* durable (지속성 설정): 교환기를 지속성 있게 만들지 여부를 결정합니다. 지속성이 설정되면 교환기가 서버 재시작 시에도 유지됩니다. (true 또는 false)
*   **exchange 생성 및 삭제 메서드**&#x20;

    ```java
    public interface Channel extends ShutdownNotifier, AutoCloseable {
    ...
      /* Actively declare a non-autodelete exchange with no extra arguments */
      Exchange.DeclareOk exchangeDeclare(String exchange, 
                        String type, 
                        boolean durable) throws IOException;
      /* Actively declare a non-autodelete exchange with no extra arguments  */
      Exchange.DeclareOk exchangeDeclare(String exchange,
                        BuiltinExchangeType type,
                        boolean durable) throws IOException;
      /* Declare an exchange. */
      Exchange.DeclareOk exchangeDeclare(String exchange, 
                        String type, 
                        boolean durable, 
                        boolean autoDelete, 
                        Map<String, Object> arguments) throws IOException;
      /*  Declare an exchange.*/
      Exchange.DeclareOk exchangeDeclare(String exchange, 
                        BuiltinExchangeType type, 
                        boolean durable, 
                        boolean autoDelete,
                        Map<String, Object> arguments) throws IOException;
      /* Declare an exchange, via an interface that allows the complete set of arguments. */
      Exchange.DeclareOk exchangeDeclare(String exchange,
                        String type, 
                        boolean durable,
                        boolean autoDelete, 
                        boolean internal,
                        Map<String, Object> arguments) throws IOException;
      /* Declare an exchange, via an interface that allows the complete set of arguments. */
      Exchange.DeclareOk exchangeDeclare(String exchange,
                        BuiltinExchangeType type,
                        boolean durable,
                        boolean autoDelete,
                        boolean internal,
                        Map<String, Object> arguments) throws IOException;
      /**
      * Like {@link Channel#exchangeDeclare(String, String, boolean, boolean, java.util.Map)} but
      * sets nowait parameter to true and returns nothing (as there will be no response from
      * the server).
      */
      void exchangeDeclareNoWait(String exchange,
                        String type,
                        boolean durable,
                        boolean autoDelete,
                        boolean internal,
                        Map<String, Object> arguments) throws IOException;
      /**
      * Like {@link Channel#exchangeDeclare(String, String, boolean, boolean, java.util.Map)} but
      * sets nowait parameter to true and returns nothing (as there will be no response from
      * the server).
      */
      void exchangeDeclareNoWait(String exchange,
                        BuiltinExchangeType type,
                        boolean durable,
                        boolean autoDelete,
                        boolean internal,
                        Map<String, Object> arguments) throws IOException;
      /* Declare an exchange passively; that is, check if the named exchange exists. */
      Exchange.DeclareOk exchangeDeclarePassive(String name) throws IOException;

      /*  Delete an exchange */
      Exchange.DeleteOk exchangeDelete(String exchange, boolean ifUnused)
                                       throws IOException;
      /* Delete an exchange, without regard for whether it is in use or not */
      Exchange.DeleteOk exchangeDelete(String exchange) throws IOException;
      void exchangeDeleteNoWait(String exchange, boolean ifUnused)
                                       throws IOException;                 
    ...
    }

    ```

### 1-3 Exchange 설정 코드

#### 1-3-1.  관리자 UI 에서 Exchange 생성 전 화면입니다.

<figure><img src="../../../.gitbook/assets/image (486).png" alt=""><figcaption><p>관리 Tool</p></figcaption></figure>

#### **1-3-2. Exchange 생성 코드**&#x20;

```java
public class CreateExchange {
    private final static Logger logger = LoggerFactory.getLogger(CreateExchange.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Create CreateExchange Start [" + CommonConfigs.DEFAULT_QUEUE + "]");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();

        // 3. Exchange 생성
        channel.exchangeDeclare("My-Direct-Exchange", BuiltinExchangeType.DIRECT, true);

        channel.close();
        connection.close();
    }
}
```

#### **1-2-3. 생성 결과 확인** &#x20;

<figure><img src="../../../.gitbook/assets/image (488).png" alt=""><figcaption></figcaption></figure>

## 2. Queue

Queue는 반드시 미리 정의되어야 하며, 이름, 내구성, 자동 삭제 여부 등의 속성을 갖습니다. RabbitMQ를 사용할 때 적절한 Queue 설정을 통해 메시지를 효율적으로 처리할 수 있습니다

* **Name**: Queue 이름으로 `amq.`은 예약어로써 사용할 수 없습니다.
* **Durability**: Queue가 브로커 재시작 시에도 남아 있는지 여부를 결정합니다
  * **Durable**: 브로커가 재시작되어도 디스크에 저장되어 남아 있습니다.
  * **Transient**: 브로커가 재시작되면 사라집니다. 단, Queue에 저장되는 메시지는 내구성을 갖지 않습니다.
* **Auto delete**: 마지막 Consumer가 consume을 끝낼 경우 자동 삭제됩니다.
* **Arguments**: 메시지 TTL, Max Length 같은 추가 기능을 명시할 수 있습니다

### 2-1.  관리자 UI

<figure><img src="../../../.gitbook/assets/image (491).png" alt=""><figcaption></figcaption></figure>

Add a new queue 영역에서 큐 정보를 입력 후 생성 합니다.

### 2-2. 프로그램 방식

#### 2-2-1.  관리자 UI를 통한 프로그램 방식으로 이전 화면

<figure><img src="../../../.gitbook/assets/image (491).png" alt=""><figcaption></figcaption></figure>

#### 2-2-2.  프로그램&#x20;

큐 생성 및 삭제는 다음 메서드를 사용 합니다.

```java
AMQP.Queue.DeclareOk queueDeclare() throws IOException;

AMQP.Queue.DeclareOk queueDeclare(String var1, boolean var2, boolean var3, boolean var4, Map<String, Object> var5) throws IOException;

void queueDeclareNoWait(String var1, boolean var2, boolean var3, boolean var4, Map<String, Object> var5) throws IOException;

AMQP.Queue.DeclareOk queueDeclarePassive(String var1) throws IOException;

AMQP.Queue.DeleteOk queueDelete(String var1) throws IOException;

AMQP.Queue.DeleteOk queueDelete(String var1, boolean var2, boolean var3) throws IOException;

void queueDeleteNoWait(String var1, boolean var2, boolean var3) throws IOException;
```

* 큐 생성 코드드

```java
public class CreateExchange {
    private final static Logger logger = LoggerFactory.getLogger(CreateExchange.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Create CreateExchange Start ");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();

        // 3. Exchange 생성
        channel.exchangeDeclare("My-Direct-Exchange", BuiltinExchangeType.DIRECT, true);

        // 4  Queue 생성
        channel.queueDeclare("My-Direct-Email-Q", true, false, false, null);
        channel.queueDeclare("My-Direct-Sms-Q", true, false, false, null);
        channel.queueDeclare("My-Direct-Sns-Q", true, false, false, null);

        channel.close();
        connection.close();
    }
}
```

#### 2-2-3.  관리자 화면을 통한 큐 생성 후 화면&#x20;

"My-Direct-xxx"로 된 3개의 큐가 생성 된 것을 확인 할 수 있습니다.

<figure><img src="../../../.gitbook/assets/image (492).png" alt=""><figcaption></figcaption></figure>

## 3. Exchange 와 Queue 바인딩

exchange와 queue 에 대한 코드를 작성하였습니다. 즉 메세지는 큐로 직접 전달이 되지 않는 라우팅 설정 입니다. Exchange는 RabbitMQ 서버 내의 가상 호스트(vhost)에 상주하는 메시지 라우팅 에이전트로 메세지를 큐로 보내기 위해서는 바인딩 규칙을 설정 해야 합니다.

```java
public interface Channel extends ShutdownNotifier, AutoCloseable {
...
  /* Bind a queue to an exchange, with no extra arguments */
  Queue.BindOk queueBind(String queue, 
                    String exchange, 
                    String routingKey) throws IOException;
  /* Bind a queue to an exchange. */
  Queue.BindOk queueBind(String queue, 
                    String exchange,
                    String routingKey, 
                    Map<String, Object> arguments) throws IOException;
  /**
  * Same as queueBind(String, String, String, java.util.Map)} but sets nowait
  * parameter to true and returns void (as there will be no response
  * from the server).
  */
  void queueBindNoWait(String queue,
                    String exchange, 
                    String routingKey,
                    Map<String, Object> arguments) throws IOException;
  /**
  * Unbinds a queue from an exchange, with no extra arguments.
  */
  Queue.UnbindOk queueUnbind(String queue, 
                    String exchange, 
                    String routingKey) throws IOException;
  /**
  * Unbind a queue from an exchange.
  */
  Queue.UnbindOk queueUnbind(String queue, 
                    String exchange, 
                    String routingKey, 
                    Map<String, Object> arguments) throws IOException;
...
}
```

### 3-1. 바인딩 전 관리자 UI

<figure><img src="../../../.gitbook/assets/image (493).png" alt=""><figcaption></figcaption></figure>

### 3-2. 바인딩 코드 추가

```java
public class CreateExchange {
    private final static Logger logger = LoggerFactory.getLogger(CreateExchange.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Create CreateExchange Start ");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();

        // 3. Exchange 생성
        channel.exchangeDeclare("My-Direct-Exchange", BuiltinExchangeType.DIRECT, true);

        // 4  Queue 생성
        channel.queueDeclare("My-Direct-Email-Q", true, false, false, null);
        channel.queueDeclare("My-Direct-Sms-Q", true, false, false, null);
        channel.queueDeclare("My-Direct-Sns-Q", true, false, false, null);

        // 5 bindings 생성  - (queue, exchange, routingKey)
        channel.queueBind("My-Direct-Email-Q", "My-Direct-Exchange", "email");
        channel.queueBind("My-Direct-Sms-Q", "My-Direct-Exchange", "sms");
        channel.queueBind("My-Direct-Sns-Q", "My-Direct-Exchange", "sns");

        channel.close();
        connection.close();
    }
}
```

### &#x20;3-3. 바인딩 코드 후 관리자 UI

<figure><img src="../../../.gitbook/assets/image (494).png" alt=""><figcaption></figcaption></figure>

## 4. Publisher/Consumer

### 4-1. Publisher 코드

```java
public class ExchangePublisher {
    private final static Logger logger = LoggerFactory.getLogger(ExchangePublisher.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Publisher Start ");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();

        // 3. 전송 메세지
        String msg = "이메일 전송 ......";

        // 4. publish  (exchange, routingKey, properties, messageBody)
        channel.basicPublish("My-Direct-Exchange", "email", null, msg.getBytes());

        channel.close();
        connection.close();
    }
}
```

### 4-2. Consumer 코드

```java
public class ExchangeConsumer {
    private final static Logger logger = LoggerFactory.getLogger(ExchangeConsumer.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Consumer :: My-Direct-Email-Q  Start ");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();
        
        // 3. Deliver 생성
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println(consumerTag);
            System.out.println(new String(message.getBody(), "UTF-8"));
        };

        // 4. Cancel 생성
        CancelCallback cancelCallback = consumerTag -> {
            System.out.println(consumerTag);
        };

        channel.basicConsume("My-Direct-Email-Q", true, deliverCallback, cancelCallback);

    }
}
```

### 4-3. Publisher/Consumer 실행&#x20;

Consumer를 먼저 실행 하고 Publisher 를 실행하여 결과를 확인합니다.&#x20;

<figure><img src="../../../.gitbook/assets/image (498).png" alt=""><figcaption></figcaption></figure>

* **My-Direct-Email-Q의 관리자 UI**

<figure><img src="../../../.gitbook/assets/image (495).png" alt=""><figcaption></figcaption></figure>

* **My-Direct-SMS-Q의 관리자 UI**

<figure><img src="../../../.gitbook/assets/image (496).png" alt=""><figcaption></figcaption></figure>

## 5. 전체 소스

{% tabs %}
{% tab title="CreateExchange" %}
```java
public class CreateExchange {
    private final static Logger logger = LoggerFactory.getLogger(CreateExchange.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Create CreateExchange Start ");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();

        // 3. Exchange 생성
        channel.exchangeDeclare("My-Direct-Exchange", BuiltinExchangeType.DIRECT, true);

        // 4  Queue 생성
        channel.queueDeclare("My-Direct-Email-Q", true, false, false, null);
        channel.queueDeclare("My-Direct-Sms-Q", true, false, false, null);
        channel.queueDeclare("My-Direct-Sns-Q", true, false, false, null);

        // 5 bindings 생성  - (queue, exchange, routingKey)
        channel.queueBind("My-Direct-Email-Q", "My-Direct-Exchange", "email");
        channel.queueBind("My-Direct-Sms-Q", "My-Direct-Exchange", "sms");
        channel.queueBind("My-Direct-Sns-Q", "My-Direct-Exchange", "sns");

        channel.close();
        connection.close();
    }
}
```
{% endtab %}

{% tab title="ExchangePublisher " %}
```java
public class ExchangePublisher {
    private final static Logger logger = LoggerFactory.getLogger(ExchangePublisher.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Publisher Start ");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();

        // 3. 전송 메세지
        String emai = "이메일 전송 ......";
        String sms = "sms 전송 ......";
        String sns = "sns 전송 ......";

        // 4. publish  (exchange, routingKey, properties, messageBody)
        channel.basicPublish("My-Direct-Exchange", "email", null, emai.getBytes());
        channel.basicPublish("My-Direct-Exchange", "sms", null, sms.getBytes());
        channel.basicPublish("My-Direct-Exchange", "sns", null, sns.getBytes());

        channel.close();
        connection.close();
    }
}
```
{% endtab %}

{% tab title="Consumer" %}
```java
public class ExchangeEmailConsumer {
    private final static Logger logger = LoggerFactory.getLogger(ExchangeEmailConsumer.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Consumer :: My-Direct-Email-Q  Start ");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();

        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println(consumerTag);
            System.out.println(new String(message.getBody(), "UTF-8"));
        };

        CancelCallback cancelCallback = consumerTag -> {
            System.out.println(consumerTag);
        };

        channel.basicConsume("My-Direct-Email-Q", true, deliverCallback, cancelCallback);

    }
}

public class ExchangeSmsConsumer {
    private final static Logger logger = LoggerFactory.getLogger(ExchangeSmsConsumer.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Consumer :: My-Direct-Sms-Q  Start ");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();

        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println(consumerTag);
            System.out.println(new String(message.getBody(), "UTF-8"));
        };

        CancelCallback cancelCallback = consumerTag -> {
            System.out.println(consumerTag);
        };

        channel.basicConsume("My-Direct-Sms-Q", true, deliverCallback, cancelCallback);

    }
}

public class ExchangeSnsConsumer {
    private final static Logger logger = LoggerFactory.getLogger(ExchangeSnsConsumer.class);

    public static void main(String[] args) throws IOException, TimeoutException {
        logger.info("RabbitMQ Consumer :: My-Direct-Sns-Q  Start ");

        // 1. Connection 생성
        Connection connection = CommonConfigs.getRabittMQConnection();

        // 2. 채널 생성
        Channel channel = connection.createChannel();

        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println(consumerTag);
            System.out.println(new String(message.getBody(), "UTF-8"));
        };

        CancelCallback cancelCallback = consumerTag -> {
            System.out.println(consumerTag);
        };

        channel.basicConsume("My-Direct-Sns-Q", true, deliverCallback, cancelCallback);

    }
}
```
{% endtab %}

{% tab title="CommonConfigs " %}
```java
public class CommonConfigs {
    

    public static Connection getRabittMQConnection() throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();


        factory.setHost("x.x.x.x");
        factory.setPort(9999);
        factory.setUsername("id");
        factory.setPassword("pwd");
        Connection connection = factory.newConnection();
        
        return connection;
    }
}
```
{% endtab %}
{% endtabs %}

* 실행 결과

<figure><img src="../../../.gitbook/assets/image (499).png" alt=""><figcaption></figcaption></figure>

## 6. Type:Direct

ConnectionFactory를 한번 생성 하기 위해 싱글톤 패턴으로 변경 소스&#x20;

```java
public class RabbitMqQConnectionManager {
    private final static Logger logger = LoggerFactory.getLogger(RabbitMqQConnectionManager.class);

    private static Connection connection = null;

    public static Connection getConnection()   {

        if (connection == null) {
            ConnectionFactory factory = new ConnectionFactory();
            factory.setHost(RabbitMQConstant.RABBITMQ_HOST);
            factory.setPort(RabbitMQConstant.RABBITMQ_PORT);
            factory.setUsername(RabbitMQConstant.RABBITMQ_ID);
            factory.setPassword(RabbitMQConstant.RABBITMQ_PWD);
            try {
                connection = factory.newConnection();
            } catch (IOException | TimeoutException e) {
                logger.error("RabbitMQ Connection Failed :: " +  e.fillInStackTrace());
                throw new RuntimeException(e);
            }
        }

        return connection;
    }
}
```

<details>

<summary>한번에 실행으로 변경 소스</summary>

```java
public class DiectExchange {

    private static Logger logger =  LoggerFactory.getLogger(DiectExchange.class);

    public static final String DIRECT_EXCHANGE = "My-Direct-Exchange";
    public static final String DIRECT_QUEUE_EMAIL = "My-Direct-Email-Q";
    public static final String DIRECT_QUEUE_SMS = "My-Direct-Sms-Q";
    public static final String DIRECT_QUEUE_SNS = "My-Direct-Sns-Q";

    public static void run() throws IOException, TimeoutException {

        exchangeDeclare();
        queueDeclare();
        queueBind();

        Thread consumer = new Thread(() -> {
            try {
                consumMessage();
            } catch (IOException | TimeoutException | InterruptedException e) {
                e.printStackTrace();
            }
        });


        Thread publisher = new Thread(() -> {
            try {
                publishMessage();
            } catch (IOException | TimeoutException | InterruptedException e) {
                e.printStackTrace();
            }
        });

        publisher.start();
        consumer.start();
    }



    private static void exchangeDeclare() throws IOException, TimeoutException {
        Channel channel =  RabbitMqQConnectionManager.getConnection().createChannel();
        channel.exchangeDeclare(DIRECT_EXCHANGE, BuiltinExchangeType.DIRECT, true);
        channel.close();
    }

    private static void queueDeclare() throws IOException, TimeoutException {
        Channel channel =  RabbitMqQConnectionManager.getConnection().createChannel();
        channel.queueDeclare(DIRECT_QUEUE_EMAIL, true, false, false, null);
        channel.queueDeclare(DIRECT_QUEUE_SMS, true, false, false, null);
        channel.queueDeclare("My-Direct-Sns-Q", true, false, false, null);
        channel.close();
    }

    private static void queueBind() throws IOException, TimeoutException {
        Channel channel = RabbitMqQConnectionManager.getConnection().createChannel();

        channel.queueBind(DIRECT_QUEUE_EMAIL, DIRECT_EXCHANGE, "email");
        channel.queueBind(DIRECT_QUEUE_SMS, DIRECT_EXCHANGE, "sms");
        channel.queueBind(DIRECT_QUEUE_SNS, DIRECT_EXCHANGE, "sns");

        channel.close();
    }

    private static void publishMessage() throws IOException, TimeoutException, InterruptedException {
        Channel channel = RabbitMqQConnectionManager.getConnection().createChannel();

        // 3. 전송 메세지
        String emai = "이메일 전송 ......";
        String sms = "sms 전송 ......";
        String sns = "sns 전송 ......";

        // 4. publish  (exchange, routingKey, properties, messageBody)
        channel.basicPublish(DIRECT_EXCHANGE, "email", null, emai.getBytes());
        Thread.sleep(1000);
        channel.basicPublish(DIRECT_EXCHANGE, "sms", null, sms.getBytes());
        Thread.sleep(1000);
        channel.basicPublish(DIRECT_EXCHANGE, "sns", null, sns.getBytes());

        channel.close();
    }

    private static void consumMessage() throws IOException, TimeoutException, InterruptedException {
        Channel channel = RabbitMqQConnectionManager.getConnection().createChannel();

        channel.basicConsume(DIRECT_QUEUE_EMAIL,
                true,
                ((consumerTag, message) -> {
                    logger.info("consumerTag : " + consumerTag);
                    logger.info(DIRECT_QUEUE_EMAIL + " : " + new String(message.getBody(), "UTF-8"));
                }),
                consumerTag -> {
                    logger.info(consumerTag);
                });

        channel.basicConsume(DIRECT_QUEUE_SMS,
                true,
                ((consumerTag, message) -> {
                    logger.info("consumerTag : " + consumerTag);
                    logger.info(DIRECT_QUEUE_SMS + " : " + new String(message.getBody(), "UTF-8"));
                }),
                consumerTag -> {
                    logger.info(consumerTag);
                });

        channel.basicConsume(DIRECT_QUEUE_SNS,
                true,
                ((consumerTag, message) -> {
                    logger.info("consumerTag : " + consumerTag);
                    logger.info(DIRECT_QUEUE_SNS + " : " + new String(message.getBody(),"UTF-8"));
                }),
                consumerTag -> {
                    logger.info(consumerTag);
                });
    }
}
```

</details>

* 결과&#x20;

<figure><img src="../../../.gitbook/assets/image (501).png" alt=""><figcaption></figcaption></figure>
