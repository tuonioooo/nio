# Selector

参考文档：

JAVA NIO中文版第四章选择器

百度链接: [https://pan.baidu.com/s/1ByltDNJqnoaKsUkEADMd6A](https://pan.baidu.com/s/1ByltDNJqnoaKsUkEADMd6A) 密码: 4fan

Selector（选择器）是Java NIO中能够检测一到多个NIO通道，并能够知晓通道是否为诸如读写事件做好准备的组件。这样，一个单独的线程可以管理多个channel，从而管理多个网络连接。

下面是本文所涉及到的主题列表：

1. [为什么使用Selector?](http://ifeve.com/selectors/#Why)
2. [Selector的创建](http://ifeve.com/selectors/#Creating)
3. [向Selector注册通道](http://ifeve.com/selectors/#Registering)
4. [SelectionKey](http://ifeve.com/selectors/#SelectionKey)
5. [通过Selector选择通道](http://ifeve.com/selectors/#Selecting)
6. [wakeUp\(\)](http://ifeve.com/selectors/#wakeUp)
7. [close\(\)](http://ifeve.com/selectors/#close)
8. [完整的示例](http://ifeve.com/selectors/#Full)



