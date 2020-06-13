# 解析器模块

主要提供两个功能

- 一个功能，是对 [XPath](http://www.w3school.com.cn/xpath/index.asp) 进行封装，为 MyBatis 初始化时解析 `mybatis-config.xml` 配置文件以及映射配置文件提供支持。
- 另一个功能，是为处理动态 SQL 语句中的占位符提供支持。



## XPathParser

基于 Java **XPath** 解析器，用于解析 MyBatis `mybatis-config.xml` 和 `**Mapper.xml`

- ```
  document
  ```

  属性，XML 被解析后，生成的

  ```
  org.w3c.dom.Document
  ```

   

  对象。

  - `validation` 属性，是否校验 XML 。一般情况下，值为 `true` 。

  - ```
    entityResolver
    ```

    ```
    org.xml.sax.EntityResolver
    ```

    对象，XML 实体解析器。默认情况下，对 XML 进行校验时，会基于 XML 文档开始位置指定的 DTD 文件或 XSD 文件。例如说，解析

    ```
    mybatis-config.xml
    ```

    配置文件时，会加载

    ```
    http://mybatis.org/dtd/mybatis-3-config.dtd
    ```

    这个 DTD 文件。但是，如果每个应用启动都从网络加载该 DTD 文件，势必在弱网络下体验非常下，甚至说应用部署在无网络的环境下，还会导致下载不下来，那么就会出现 XML 校验失败的情况。所以，在实际场景下，MyBatis 自定义了 EntityResolver 的实现，达到使用本地DTD 文件，从而避免下载网络DTD 文件的效果。详细解析，见

    

- `xpath` 属性，`javax.xml.xpath.XPath` 对象，用于查询 XML 中的节点和元素。如果对 XPath 的使用不了解的胖友，请先跳转 [《Java XPath 解析器 - 解析 XML 文档》](https://www.yiibai.com/java_xml/java_xpath_parse_document.html) 中，进行简单学习，灰常简单。

- `variables` 属性，变量 Properties 对象，用来替换需要动态配置的属性值。例如：

  ```
  <dataSource type="POOLED">
    <property name="driver" value="${driver}"/>
    <property name="url" value="${url}"/>
    <property name="username" value="${username}"/>
    <property name="password" value="${password}"/>
  </dataSource>
  ```

  - `variables` 的来源，即可以在常用的 Java Properties 文件中配置，也可以使用 MyBatis `<property />` 标签中配置。例如：

    ```
    <properties resource="org/mybatis/example/config.properties">
      <property name="username" value="dev_user"/>
      <property name="password" value="F2Fa3!33TYyg"/>
    </properties>
    ```

    - 这里配置的 `username` 和 `password` 属性，就可以替换上面的 `${username}` 和 `${password}` 这两个动态属性。
    - 具体如何实现的，我们来看下面的 `PropertyParser#parse(String string, Properties variables)` 方法。



