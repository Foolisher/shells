

# ClassLoader
类加载器就是用来将java字节码加载到jvm的工具。来加载器的资源可以来自内存、网络、内存甚至内存。不同的类加载器即使加载了相同的Class，这两个Class也不能相互赋值及转换的，JVM不认为他们是相同的类。若希望实现自己的类加载器，可以继承java.lang.ClassLoader。

类加载器分为三类：

1. **引导类加载器（bootstrap class loader）**
  负责加载jdk核心库，如 rt.jar，其特点是用原生代码实现的

2. **Extention ClassLoader**
  负责加载jdk扩展依赖 ext/*.jar 

3. **App ClassLoader**
  应用类加载器，加载除jdk外的Class
  
除了引导启动加载器外，其他的加载器都有