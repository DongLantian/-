1. 使用Spring Initializr创建Springboot项目

![1557397028207](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1557397028207.png)

2. 填写项目名等信息（Project Metadata）。

   ![1557397265681](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1557397265681.png)

3. 选择要集成的组件

   ![1557397365708](springboot\3.png)

4. 选择工作空间，完成。

5. 因为选择了集成Mybatis和MySQL，所以要配置数据库。

   在application.properties中配置。

   ```java
   #datasource
   spring.datasource.url=jdbc:mysql://localhost:3306/xining?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=true
   spring.datasource.username=root
   spring.datasource.password=123
   spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
   ```

6. 然后就可以写个hello world！！！开心的跑起来了。







