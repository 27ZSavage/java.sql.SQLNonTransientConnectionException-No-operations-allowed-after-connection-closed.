![image](https://user-images.githubusercontent.com/117962284/201256606-eaae97c1-8761-4279-a667-ac5e85a2ed00.png)

声明 ：此异常在我本身项目中的出现，可能和别人的原因不一样。

 今天用serlvet连接数据库的时候，执行项目时出现java.sql.SQLNonTransientConnectionException: No operations allowed after connection closed.

以及ConnectionIsClosedException: No operations allowed after connection closed.

的异常信息，困扰了我很久。

1.先看报错的，位置94行，由此应该可以知道是我DBUtils工具类的问题
![image](https://user-images.githubusercontent.com/117962284/201256655-a3cf2985-a1af-47c7-a9d1-359c60380d93.png)
2.我是用了TheadLocal类来（TheadLocal是用Map集合来绑定数据的） 同步事务，也就是要求同一个conn对象，使用的tomcat

（我这个项目就是简单的转账项目 然后我在网上查了查资料后，看到了tomcat 会在底层创建线程池对象。假如现在tomcat线程池里面有 t1 t2 t3 这三个创建好的线程（默认创建的，等我们使用）

![image](https://user-images.githubusercontent.com/117962284/201256713-dae2d1b6-71ac-45ae-aea2-9cbb3be7cdaa.png)
![image](https://user-images.githubusercontent.com/117962284/201256735-29d87795-9015-47f8-9f0f-bb9025f6f808.png)
3.当我们第一用了 t1这个线程的时候 ，并且使用完之后 conn对象关闭，但（TheadLocal）map集合 中 t1，conn 键值对我们并没有移除。现在如果有另一个人 也从tomcat线程池中拿到t1线程，但这时候 我们map中的键值对并没有删除，他拿到线程t1的时候，拿到的conn是个关闭的conn。

4.这时就会抛出异常。所以解决方案就是，在我们DBUtils 工具类关闭conn连接对象的时候，一定要从map里面移除键值对， 也就是用 xxx.remove()，这样这个问题就会得到解决。

![image](https://user-images.githubusercontent.com/117962284/201256795-04fe5dfc-3f84-474b-9b3e-d31fe479746a.png)
结语：时刻记住删除键值对。

                                                                               ps：第一次写博客，菜鸡一个，大家多多包涵0.0。我也不知道我说的是不是对的，如果有错误的话，希望大家多多包涵，指出错误。感谢！！
                                                                                      I'm a rookie
