

# ClassLoader
类加载器就是用来将java字节码加载到jvm的工具。来加载器的资源可以来自内存、网络、内存

类加载器分为三类：

1. **Bootstrap ClassLoader**
  负责加载jdk基础类，如 rt.jar 

2. **Extention ClassLoader**
  负责加载jdk扩展依赖 ext/*.jar 

3. **App ClassLoader**
  应用类加载器，加载除jdk外的Class