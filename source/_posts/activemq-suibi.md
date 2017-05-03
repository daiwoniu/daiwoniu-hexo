---
title: ActiveMQ随笔
tags:
  - jave
  - activemq
  - jms
  - 队列
  - 随笔
date: 2017-05-03 17:32:23
updated: 2017-05-03 17:32:23
categories: java
---
### 2017-05-03 17:32:23
主要记录下如何通过java操作ActiveMQ消息队列，都是些最初级的使用，\(^o^)/~
<!--more-->
###### 方式一：
直接通过activemq基础方式来操作mq队列
1. 依赖
 建议使用maven方式引入依赖，如就是不想用maven方式，请结合图片自行下载相关jar，或联系本博主
 ![方式一依赖](/img/activemq-suibi/1.png)

2. 主要java代码
   - 初始化链接工厂
   > // 连接地址
     private static final String mqURL = "tcp://127.0.0.1:61616";
     // 连接账号,
     // 默认是不需要用户名密码直接链接的
     // 需要mq修改配置支持通过账号密码链接
     private static final String mqUser = "admin";// ActiveMQConnection.DEFAULT_USER;
     // 连接密码
     private static final String mqPwd = "admin";// ActiveMQConnection.DEFAULT_PASSWORD;
     // ConnectionFactory ：连接工厂，用它创建连接
     // 构造ConnectionFactory实例对象，根据用户名，密码，url创建一个连接工厂
     ** ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(mqUser, mqPwd, mqURL); ** 
   - 从工厂得到连接对象
   > // Connection ：客户端到MQ的连接
   // 构造从工厂得到连接对象
   ** Connection connection = connectionFactory.createConnection(); ** 
   - 创建一个session对象
   >  // Session： 一个发送或接收消息的线程
    //创建一个session
    //第一个参数:是否支持事务，如果为true，则会忽略第二个参数，被jms服务器设置为SESSION_TRANSACTED
    //第一个参数为false时，第二个参数的值可为Session.AUTO_ACKNOWLEDGE，Session.CLIENT_ACKNOWLEDGE，DUPS_OK_ACKNOWLEDGE的其中一个。
    //Session.AUTO_ACKNOWLEDGE为自动确认，客户端发送和接收消息不需要做额外的工作。哪怕是接收端发生异常，也会被当作正常发送成功。
    //Session.CLIENT_ACKNOWLEDGE为客户端确认。客户端接收到消息后，必须调用javax.jms.Message的acknowledge方法。mq服务器才会当作发送成功，并删除消息。
    //DUPS_OK_ACKNOWLEDGE允许副本的确认模式。一旦接收方应用程序的方法调用从处理消息处返回，会话对象就会确认消息的接收；而且允许重复确认。
    ** Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE); ** 
   - 创建一个到达的目的地对象
   >  // Destination ：消息的目的地;消息发送给谁.
    // 其实就是连接到哪个队列
    // 如果是点对点，那么它的实现是Queue
    // 如果是订阅模式，那它的实现是Topic
    //创建一个到达的目的地，因为activemq不可能同时只能跑一个队列，这里就是连接了一个名为"Queues_Test"的队列，消息将会送到这个队列
    // 当然，如果这个队列不存在，将会被创建
    ** Destination destination = session.createQueue("Queues_Test"); ** 
   - 得到消息生成者来发送消息
   > // 得到消息生成者【发送者】
    ** MessageProducer producer = session.createProducer(destination); ** 
    // 设置生产者的模式，有两种可选
    //DeliveryMode.PERSISTENT 当activemq关闭的时候，队列数据将会被保存
    //DeliveryMode.NON_PERSISTENT 当activemq关闭的时候，队列里面的数据将会被清空
    ** producer.setDeliveryMode(DeliveryMode.PERSISTENT); ** 
    // 消息的优先级。0-4为正常的优先级，5-9为高优先级
    // producer.setPriority(5);
    // 设置true，message才可以自定义Timestamp
    // producer.setDisableMessageTimestamp(true);
    // 设置该消息的超时时间毫秒，默认值为0，表示消息永不过期，过期的消息会从队列中清除，并存储到ActiveMQ.DLQ这个队列里面。
    // producer.setTimeToLive(1000);
    //创建一个文本消息
    ** TextMessage message = session.createTextMessage("ActiveMq 发送的消息：" + i); ** 
    message.setJMSType("String"); // 自定义类型
    message.setJMSTimestamp(System.currentTimeMillis()); // 以自定义Timestamp
    message.setJMSCorrelationID("CorrelationID:" + i);
    // 如果这个值为true，表示消息是被重新发送了。
    // 因为有时消费者没有确认他已经收到消息或者提供者不确定消费者是否已经收到。
    // message.setJMSRedelivered(true);
    // 发送消息
    ** producer.send(message); ** 
   - 得到消费者来接收消息
   > // 得到消费者【消息接收者】 方式一
    ** MessageConsumer consumer = session.createConsumer(destination); ** 
    // 获取一个文本消息
    ** TextMessage message = (TextMessage) consumer.receive(10); ** // 获取消息等待毫秒数
    System.out.println("收到消息" + ** message.getText() **);
    *** 下面这种方式必须在发送消息前就设置好监听 ***
    // 得到消费者【消息接收者】 方式二
    MessageConsumer consumer = session.createConsumer(destination);
    // 通过监听自动接收消息
    consumer.setMessageListener(new MessageListener() {
	@Override
	public void onMessage(Message message) {
	    try {
		//获取到接收的数据
		String text = ((TextMessage)message).getText();
		System.out.println("收到消息" + text);
	    } catch (JMSException e) {
		e.printStackTrace();
	    }
	}
    });
	

###### 方式二：
和spring-jms整合，通过配置交由spring来管理操作mq队列
1. 依赖
	建议使用maven方式引入依赖，如就是不想用maven方式，请结合图片自行下载相关jar，或联系本博主
	![方式二依赖](/img/activemq-suibi/2.png)

2. 主要spring配置
  - 配置链接工厂：
	> <\!\-- ActiveMQ 连接工厂 \-->
	<\!\-- 真正可以产生Connection的ConnectionFactory，由对应的 JMS服务厂商提供 \-->
	<\!\-- 如连接网络：tcp://ip:61616 以及用户名，密码 \-->
	<\!\-- &lt;amq:connectionFactory id="amqConnectionFactory" brokerURL="$\{activemq.brokerURL\}" userName="$\{activemq.userName\}" password="$\{activemq.password\}"  /> \-->
	** &lt;bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
	  &lt;property name="brokerURL" value="${activemq.brokerURL}" />
	  &lt;property name="userName" value="${activemq.userName}" />
	  &lt;property name="password" value="${activemq.password}" />
	&lt;/bean> **
	&lt;!\-- Spring Caching连接工厂 \-->
	&lt;!\--connectionFactory是Spring用于创建到JMS服务器链接的，Spring提供了多种connectionFactory，常用的两个SingleConnectionFactory和CachingConnectionFactory。\-->
	&lt;!\--SingleConnectionFactory：对于建立JMS服务器链接的请求会一直返回同一个链接，并且会忽略Connection的close方法调用。\-->
	&lt;!\--CachingConnectionFactory：继承了SingleConnectionFactory，所以它拥有SingleConnectionFactory的所有功能，同时它还新增了缓存功能，它可以缓存Session、MessageProducer和MessageConsumer \-->
	** &lt;bean id="connectionFactory" class="org.springframework.jms.connection.CachingConnectionFactory"> **
	  &lt;!\-- 目标ConnectionFactory对应真实的可以产生JMS Connection的ConnectionFactory \-->
	  ** &lt;property name="targetConnectionFactory" ref="targetConnectionFactory" /> **
	  &lt;!\-- Session缓存数量 \-->
	  ** &lt;property name="sessionCacheSize" value="${activemq.sessionCacheSize}" /> **
	** &lt;/bean> ** 
  - 配置队列目的地
  >  &lt;!\--这个是队列目的地 \-->
   ** &lt;bean id="defaultQueueDestination" class="org.apache.activemq.command.ActiveMQQueue">
     &lt;constructor-arg>
       &lt;value>${activemq.defaultDestinationName}&lt;/value>
     &lt;/constructor-arg>
    &lt;/bean> **
    &lt;!\-- 定义JmsTemplate的Queue队列类型 -->
    &lt;!\-- Spring提供的JMS工具类，它可以进行消息发送、接收等 \-->
    ** &lt;bean id="jmsQueueTemplate" class="org.springframework.jms.core.JmsTemplate"> **
    &lt;!\-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 \-->
      ** &lt;constructor-arg ref="connectionFactory" />
      &lt;property name="defaultDestination" ref="defaultQueueDestination" /> **
      &lt;!\-- 非pub/sub模型（发布/订阅），即点对点队列模式 \-->
      ** &lt;property name="pubSubDomain" value="false" />
      &lt;property name="receiveTimeout" value="${activemq.receiveTimeout}" />
      &lt;property name="deliveryMode" value="${activemq.deliveryMode}" />
    &lt;/bean> **
  - 配置消息监听适配器
  >  &lt;!\-- 消息监听适配器,支持三种方式，只配置其中一种即可 \-->
   &lt;!\-- 方式一：单纯接收消息，依赖接口，如果要回复或者发送消息要在代码里面去重新获取一个Connection或Session \-->
   ** &lt;bean id="myMessageListener" class="mq.MyMessageListener" /> **
   &lt;!\-- 方式二：接收到消息，依赖接口，参数包含session，并且可以通过session直接回复或者发送消息 \-->
   ** &lt;bean id="mySessionAwareMessageListener" class="mq.MySessionAwareMessageListener">
     &lt;property name="destination" ref="defaultQueueDestination" />
   &lt;/bean> **
   &lt;!\-- 方式三：接收消息，不依赖接口，可以自定义处理消息方法，如果方法返回值有内容，会自动回复或发送消息 \-->
   ** &lt;bean id="myMessageListenerAdapter" class="org.springframework.jms.listener.adapter.MessageListenerAdapter">
     &lt;constructor-arg>
       &lt;bean class="mq.MyMessageListenerAdapter"/>
     &lt;/constructor-arg> **
     &lt;!\--处理消息的方法 \-->
     ** &lt;property name="defaultListenerMethod" value="receiveMessageObj"/> **
     &lt;!\--回复或者发送消息的目标地址 \-->
     ** &lt;property name="defaultResponseDestination" ref="defaultQueueDestination"/>
   &lt;/bean> **
   &lt;!\-- 定义Queue监听器 \-->
   ** &lt;jms:listener-container destination-type="queue" container-type="default" connection-factory="connectionFactory" acknowledge="auto">
     &lt;jms:listener destination="${activemq.defaultDestinationName}" ref="myMessageListener"/>
     &lt;jms:listener destination="Queues_Test2" ref="mySessionAwareMessageListener"/>
     &lt;jms:listener destination="Queues_Test3" ref="myMessageListenerAdapter" />
   &lt;/jms:listener-container> **

3. 主要java代码
  - 定义消息监听器：
  > // 方式一
  import javax.jms.JMSException;
  import javax.jms.Message;
  import javax.jms.MessageListener;
  import javax.jms.TextMessage;
  /\*\*
   \* 单纯接收消息，如果要回复或者发送消息要在代码里面去重新获取一个Connection或Session
   \* Created by daiwoniu on 2017/4/21.
  \*/
  public class MyMessageListener implements MessageListener {
    @Override
    public void onMessage(Message message) {
  TextMessage textMsg = (TextMessage) message;
  try {
    System.out.println("MyMessageListener接收到消息内容是：" + textMsg.getText());
  } catch (JMSException e) {
    e.printStackTrace();
  }
    }
  }
  // 方式二
import org.springframework.jms.listener.SessionAwareMessageListener;
import javax.jms.*;
  /\*\*
   \* 接收到消息，并且可以通过session直接回复或者发送消息
   \* Created by daiwoniu on 2017/4/21.
  \*/
public class MySessionAwareMessageListener implements SessionAwareMessageListener<TextMessage> {
@Override
public void onMessage(TextMessage message, Session session) throws JMSException {
System.out.println("MySessionAwareMessageListener收到消息内容是：" + message.getText());
// 发送消息
MessageProducer producer = session.createProducer(destination);
Message textMessage = session.createTextMessage("ConsumerSessionAwareMessageListener。。。");
producer.send(textMessage);
}
private Destination destination;
public Destination getDestination() {
return destination;
}
public void setDestination(Destination destination) {
this.destination = destination;
}
}
  // 方式三
  /\*\*
   \* 接收消息，不依赖接口
   \* 如果返回值有内容，会自动回复或发送消息
   \* Created by daiwoniu on 2017/4/21.
  \*/
public class MyMessageListenerAdapter {
    /\*\*
     \* 如果没有指定接收处理的方法，默认调用handleMessage方法
     \* @param message
     \*/
    public void handleMessage(String message) {
        System.out.println("MyMessageListenerAdapter通过handleMessage接收到一个纯文本消息，消息内容是：" + message);
    }
    public void receiveMessage(String message) {
        System.out.println("MyMessageListenerAdapter通过receiveMessage接收到一个纯文本消息，消息内容是：" + message);
    }
    public String  receiveAndSendMessage(String message) {
        System.out.println("MyMessageListenerAdapter通过receiveAndSendMessage接收到一个纯文本消息，消息内容是：" + message);
        return "这是MyMessageListenerAdapter对象的receiveAndSendMessage方法的返回值。";
    }
    // MessageEntity是自己定义的实体类
    public void receiveMessageObj(MessageEntity message) {
        System.out.println("MyMessageListenerAdapter通过receiveMessageObj接收到一个纯文本消息，消息内容是：" + message.toJSON());
    }
}
  - 发送消息
  > // 获取spring的jms队列处理类
  JmsTemplate jmsQueueTemplate = SpringUtil.getAc().getBean("jmsQueueTemplate", JmsTemplate.class);
  // 发送，send方法参数有很多种，可以自行查看
  jmsQueueTemplate.send(new MessageCreator() {
    @Override
    public Message createMessage(Session session) throws JMSException {
	return session.createTextMessage("哈哈哈，终于写完了");
    }
});

从MQ页面管理后台查看的消息
![消息内容](/img/activemq-suibi/3.png)