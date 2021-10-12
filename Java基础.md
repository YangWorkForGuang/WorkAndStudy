# 项目

## VO、DTO、DO、PO

[浅析VO、DTO、DO、PO的概念、区别和用处](https://blog.csdn.net/zjrbiancheng/article/details/6253232?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-2.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-2.no_search_link)

> 比较全面，但是有些内容自己还是理解不了，还需要在项目中进一步的分析。
>
> 下面谈谈我的理解

- PO(persistence object):用于持久化时(例如保存到数据库或者缓存);
- VO(value object):用于前端展示使用(例如放置到JSP中解析或者给前端传递数据)
- DTO(data transfer object):用于接口互相调用返回,数据传输(例如很多接口调用返回值或消息队列内容);

## 注解
以etgc项目为例，分层次、分模块展示常用注解的含义。

**controller**
- @RestController：注解是@Controller和@ResponseBody的合集,表示这是个控制器bean,并且是将函数的返回值直接填入HTTP响应体中,是REST风格的控制器。

  > Java Bean: 符合一定规范编写的Java类，不是一种技术，而是一种规范。大家针对这种规范，总结了很多开发技巧、工具函数。符合这种规范的类，可以被其它的程序员或者框架使用。
  > 1. 所有属性为private
  >  2. 提供默认构造方法
  >  3. 提供getter和setter
  >  4. 实现serializable接口
- @RequestMapping：@RequestMapping(“/path”)表示该控制器处理所有“/path”的URL请求。
- @Autowired：自动导入依赖的bean
- @PostMapping是一个复合注解，作为@RequestMapping(value="/abc" , method = RequestMethod.POST)的快捷方式。也就是可以简化成@PostMapping(value="/abc" )即可，主要是方便识记。
- @RequestBody主要用来接收前端传递给后端的json字符串中的数据的(请求体中的数据的)
  - json字符串中，如果value为""的话，后端对应属性如果是String类型的，那么接受到的就是""，如果是后端属性的类型是Integer、Double等类型，那么接收到的就是null。
  - json字符串中，如果value为null的话，后端对应收到的就是null。
- @PathVariable 可以将 URL 中占位符参数绑定到控制器处理方法的入参中
![](https://img2018.cnblogs.com/blog/1576270/201901/1576270-20190129101029309-395038845.png)

**Dao**
> @Param的使用还是有一定的争议，但是目前的用法没有错。
- @Param 注解的作用是给参数命名,参数命名后就能根据名字得到参数值,正确的将参数传入sql语句中（一般通过#{}的方式，${}会有sql注入的问题）。
  - Mapper接口方法：
  ```
  public int getUsersDetail(@Param("userid") int userid);
  ```
  - 对应Sql Mapper.xml文件：
  ```
  <select id="getUserDetail" statementType="CALLABLE" resultMap="baseMap">
          Exec WebApi_Get_CustomerList #{userid}
  </select>
  ```

**POJO**
- @Data：注解在类上；提供类所有属性的 getting 和 setting 方法，此外还提供了equals、canEqual、hashCode、toString 方法
- @NotEmpty根据JDK源码注释说明，该注解只能应用于char可读序列(可简单理解为String对象),colleaction,map,array上，因为该注解要求的是对象不为null且size>0，所以只有上述对象是拥有size属性的，而Integer,Long等基础对象包装类没有该属性。

- @NotNull,表示不能为null，但可以为empty,与@NotEmpty注解相比是少了size属性，所以"Accepts any type"可以接受任何类型对象。（自我理解：就是数值类型）

- @NotBlank，"Accepts {@code CharSequence}"表明只应用于char值可读序列，则可以简单理解为只用于String，且不能为null，"non-whitespace"表示不能是空白字符，所以校验字符串是调用trim()方法之后的字符串长度大于0
- @NoArgsConstructor：注解在类上；为类提供一个无参的构造方法
- @AllArgsConstructor：注解在类上；为类提供一个全参的构造方法
- @Builder注解：
  ```
  @Builder
  public class Card {
      private int id;
      private String name;
      private boolean sex;
  }
  Card card = Card.builder().id(10).name("dasd").sex(true).build();
  ```

**service**
- @Service:一般用于修饰service层的组件
- @Log4j：注解在类上；为类提供一个属性名为log的log4j日志对像
- @Slf4j: 同上
- @Transactional 事务

**其他**
- @Component：泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。



## 包命名规范

**java中的打包机制是为了防止程序多个地方出现相同的名字而将局部程序限定在一块的机制** 如不同地区存在 同名同姓的人，为解决这个问题，我们不同地方的所有人（程序）分别打包。调用A的时候分别带上a.A或者是b.A。这样就不会出错了。 打包其实就是新建了一个文件夹，然后把需要打包的程序放在这个文件夹下面。

**要注意**：

1. **package必须是程序中可执行的第一行代码**
2. **package语句只能有一句**
3. package命名要求包含的**所有字符均为小写**，同时**不能有特殊字符**
4. **package可以有多层**，每一层有.隔开，例如：package china.hubei.wuhan;（China是一个文件夹，hubei是china下的一个文件夹，wuhan是hubei文件夹下的一个文件夹
5. package语句后面的**分号不要掉**。
6. 包的路径符合所开发的系统模块的定义，比如生产对生产，物资对物资，基础类对基础类。
7. 如果定义类的时候没有使用package,那么java就认为我们所定义的类位于**默认包**里面(default package)。

------

- **个人的项目命名**
  - **indi** ： 个体项目（individual），指个人发起，但非自己独自完成的项目，可公开或私有项目，copyright主要属于**发起者**。 包名为“`indi.发起者名.项目名.模块名……`”
  - **onem** ： 单人项目（one-man），推荐用**indi**，指个人发起，但非自己独自完成的项目，可公开或私有项目，copyright主要属于**发起者**。 包名为“`onem.发起者名.项目名.模块名……`”
  - **pers** ： 个人项目（personal），指个人发起，独自完成，可分享的项目，copyright主要属于**个人**。 包名为“`pers.个人名.项目名.模块名.……`”
  - **priv** ： 私有项目（private），指个人发起，独自完成，非公开的私人使用的项目，copyright属于**个人**。 包名为“`priv.个人名.项目名.模块名.……`”

------

- **团体的项目命名**
  - **team**： 团队项目，指由团队发起，并由该团队开发的项目，copyright属于该团队所有。 包名为“`team.团队名.项目名.模块名.……`”
  - **com** ： 公司项目，copyright由项目发起的公司所有。 包名为“`com.公司名.项目名.模块名.……`”