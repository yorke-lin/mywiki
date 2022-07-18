## NoClassDefFoundError: javax/xml/bind/*
原因：缺少jaxb-api包导致的。在Java 8以及以前的版本，是默认包含jaxb这个jar包的；但是在java SE 9.0就不再包含着个包了
解决：吧jdk换到8或引入jaxb-api包
```xml
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-impl</artifactId>
    <version>2.3.0</version>
</dependency>
```

