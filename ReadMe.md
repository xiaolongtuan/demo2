# Hibernate示例项目

Hibernate is used to deal with Object-Relationship Mapping(ORM), it bring great convenience to our programming.

It makes us focus on the program logic rather than confusing with those tables in the database.

一个Hibernate的示例项目，我还在写，完整写好后我会Push到这个项目里面。

Hibernate版本4.3.10.Final，jar包放在3rd_party内，导入项目自动/手动引入

##0、几点说明
不使用Hibernate自动建表，因为：

* 只能创建表不能创建数据库

* 创建的表的数据类型、长度经常跟我们实际需要不符。

* 缺少初始化数据。

##1、使用注解方式配置实体类
Hibernate4支持注解的方式配置实体类，免去很多XML配置文件的编写(其实从3.x版本开始就已经支持注解，但4.x进行了部分修改，配置使用更简单)。
而且Hibernate的注解是基于JPA，非侵入式注解，可以与其他注解兼容。

##2、非侵入式
Hibernate是非侵入式的，实际的代码里面可用可不用，引入其jar包后部署项目，直接启动不会报错。（Struts2与Spring不是，因为启动时就要加载一些配置，如果找不到配置文件的话启动Tomcat时就会报错）

Hibernate的配置文件hibernate.cfg.xml默认位置是classpath，也就是代码根目录src（实际目录是编译后的classes目录内），使用默认的无参函数Configuration().configure()即可获取Hibernate的Configuration对象。

但有时我们为了方便管理各种配置文件，需要自定义hibernate.cfg.xml位置，比如这个项目我将hibernate.cfg.xml放到WEB-INF/hibernate下。
那么在构造Configuration对象时就需要指定配置文件位置（详细代码见util.HibernateUtil.java）：

String CONFIG_FILE_LOCATION = "../hibernate/hibernate.cfg.xml";
Configuration cfg = new Configuration().configure(CONFIG_FILE_LOCATION);

"../hibernate/hibernate.cfg.xml"
因为默认是classpath，即 /WEB-INF/classes，所以通过上面这个位置就找到了配置文件位置，从而成功初始化Configuration对象。

PS：Hibernate4.3版本sessionFactory的获取有所不同，详见util.HibernateUtil.java

##3、Hibernate的好处都有啥？
最突出的就是ORM，这个示例中并没有使用太过复杂的映射，只是简单将一个实体类映射到一张表上。

然而实际可以实现一对多、多对一、多对多的映射，隐去数据库的细节，对象层次操作实体关系，使程序重点关注对象之间的逻辑。

而且Hibernate提供了较JDBC更加方便的操作数据的方法。
比如对象的持久化只需要一句save/delete/saveOrUpdate

对象的查询，有三种方式：

* 传统SQL方式createSQLQuery

* HQL方式createQuery

* Criteria方式createCriteria

createSQLQuery可通过addEntity将结果集与实体类关联,只需简单转换即可获得实体类集合。

createQuery、createCriteria方式更直接，不需要再addEntity即可直接转换得到实体类集

其中createCriteria可直接通过唯一标识符Id来取到对象，也可以通过添加Restrictions即查询条件来获取想要的结果集等。较进阶的连接查询等也可以通过addJoin等方式实现。


特别与Spring集成之后通过Spring提供的HibernateTemplate，根本不需要进行转换即可获取对象集合。

##4、执行流程

Jsp->Servlet->Service->Dao->Hibernate

Jsp页面发出请求进入Servlet（后台入口）

Servlet调用Service层接口（业务访问接口）

Service层调用Dao层接口（数据访问接口）

Dao的具体实现通过Hibernate提供的ORM对实体类进行操作

(hibernate.cfg.xml中指定了mapping class，而对应的class注解方式配置ORM，免去hbm.xml文件。
HibernateUtil里面读取hibernate.cfg.xml初始化ORM，并提供Session Transaction来操作数据)


