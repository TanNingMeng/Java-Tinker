## Java I/O

Java I/O 系统虽然是很基础的知识，但是理解理解起来并不容易，因为 I/O 整个体系对应的实现类实在是太多了。I/O 对应着 In 和 Out 的缩写。如果放在计算机上面，相信你们很快就能联想到了计算机与计算机交互的时候数据的读和写。例如说硬盘的读写，集合框架的读写，Socket 网路交互的读写等等。 



实际上，I 代表着<u>从不同起源地产生输入的数据</u>，而 O 代表着<u>我们产生的数据究竟要往哪里去</u>。在 Java 中，I/O 的细分的颗粒度有：

1. 方向：输入和输出
2. 类型：字符流和字节流
3. 实现：文件[输入输出] / 字节[输入输出] / String 对象



## InputStream 
