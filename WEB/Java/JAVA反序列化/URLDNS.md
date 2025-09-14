学习后的一点思考：本条链子其实就是通过new一个HashMap对象，往其中put一个对象，通过改一写东西就会调用该对象的hashcode函数



用处：本条链子可以用来请求url

在HashMap的readObject的最后将会调用HashMap的hash函数

首先是HashMap的readObject方法，会调用HashMap的putVal，参数有HashMap的hash()函数

```java
static final int hash(Object key) {  
    int h;  
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  
}
```

当不为null的时候会调用所传入对象的hashcode()函数，因为这里是调用了URL类的hashcode()，不能直接跟进，而应该从URL类中进行查找

```java
public synchronized int hashCode() {  
    if (hashCode != -1)  
        return hashCode;  
  
    hashCode = handler.hashCode(this);  
    return hashCode;  
}
```

而URhashcode不等于1就会调用handler中hashcode函数

```java
transient URLStreamHandler handler;
```
handler为URLStreamHandler类的变量

```java
InetAddress addr = getHostAddress(u);
```
仔细观察后发现有getHostAddress(u)，继续跟进

```java
synchronized InetAddress getHostAddress() {
    // 检查是否已经有主机 IP 地址缓存，如果有的话直接返回
    if (hostAddress != null) {
        return hostAddress;
    }
    // 如果主机名为空或者是空字符串，返回 null，表示无效输入
    if (host == null || host.isEmpty()) {
        return null;
    }
    try {
        // 尝试通过主机名获取 InetAddress（IP 地址）对象
        hostAddress = InetAddress.getByName(host);
    } catch (UnknownHostException | SecurityException ex) {
        // 如果发生 UnknownHostException（主机名无效）或 SecurityException（安全异常），返回 null
        return null;
    }
    // 返回获取到的主机 IP 地址
    return hostAddress;
}
```

InetAddress.getByName()函数即是通过主机名获取IP，也就是会发送请求

所以链子为：

HashMap
->HashMap.readObject()
->hash()->URL.hascode()
->(URLStreamHandler)handler.hashcode()
->getHostAddress(u)
->InetAddress.getByName(host)





此外：

HashMap类中的put函数也会调用hash函数，有些类可能会继承其put函数，也可能会造成hashcode可控

```
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```
