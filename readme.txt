Hive使用Mysql存储元数据

继上一篇使用Derby存储


一、mysql新建数据库hive
10.5.2.241/hive

二、修改hive配置文件hive-site.xml
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://10.5.2.241:3306/hive?createDatabaseIfNotExist=true&amp;useSSL=false</value>
    <description>
      JDBC connect string for a JDBC metastore.
      To use SSL to encrypt/authenticate the connection, provide database-specific SSL flag in the connection URL.
      For example, jdbc:postgresql://myhost/db?ssl=true for postgres database.
    </description>
  </property>

<property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
    <description>Driver class name for a JDBC metastore</description>
  </property>

<property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>root</value>
    <description>Username to use against metastore database</description>
  </property>

<property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>root</value>
    <description>password to use against metastore database</description>
  </property>

三、进入lib目录，下载mysql驱动包
wget http://central.maven.org/maven2/mysql/mysql-connector-java/5.1.41/mysql-connector-java-5.1.41.jar

四、初始化Mysql数据库表
cd /usr/local/hive/bin
schematool-initSchema -dbType mysql
当出现“schemaTool completed”，表明已经初始化成功

五、查看Mysql的hive数据库
多了57张表

六、启动hive
cd /usr/local/hive/bin
hive

七、在Hive中创建表
cd /usr/local/hive
hive
show databases;
use default;
create table word_count(line string);
show tables;
select * from word_count;

八、观察hadoop fs -ls /user，准备HDFS相应测试数据，上传到/wcinput
hadoop fs -ls /
hadoop fs -mkdir /wcinput/
hadoop fs -put /usr/local/hadoop/hadoop_temp/3.txt /wcinput

九、hive加载数据处理
load data inpath '/wcinput' overwrite into table word_count;

十、hive执行相应操作，查看执行结果（执行结果放在新创建表word_count_result中）
select * from word_count;
select split(line, ' ') from word_count;
select explode(split(line, ' ')) from word_count;
select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word;
select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word order by count;
create table word_count_result as select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word order by count;
select * from word_count_result;

