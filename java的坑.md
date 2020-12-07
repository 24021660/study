## 使用jmx
```java
private String url="service:jmx:rmi:///jndi/rmi://localhost:11099/jmxrmi";
private String broker="org.apache.activemq:brokerName=localhost,type=Broker";
 
public static void main(String[] args) throws IOException, MalformedObjectNameException {
        JMXServiceURL urls = new JMXServiceURL(url);
        JMXConnector connector = JMXConnectorFactory.connect(urls,null);
        connector.connect();
        MBeanServerConnection conn = connector.getMBeanServerConnection();
        ObjectName name = new ObjectName(broker);
        BrokerViewMBean mBean = (BrokerViewMBean)MBeanServerInvocationHandler.newProxyInstance(conn, name, BrokerViewMBean.class, true);
        for(ObjectName na : mBean.getQueues()){//获取点对点的队列       mBean.getTopics() 获取订阅模式的队列
            QueueViewMBean queueBean = (QueueViewMBean) 
            MBeanServerInvocationHandler.newProxyInstance(conn, na, QueueViewMBean.class, true);
            System.out.println("******************************");
            System.out.println("队列的名称："+queueBean.getName());
            System.out.println("队列中剩余的消息数："+queueBean.getQueueSize());
            System.out.println("消费者数："+queueBean.getConsumerCount());
            System.out.println("出队列的数量："+queueBean.getDequeueCount());
        }
```