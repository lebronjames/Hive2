Hiveʹ��Mysql�洢Ԫ����

����һƪʹ��Derby�洢


һ��mysql�½����ݿ�hive
10.5.2.241/hive

�����޸�hive�����ļ�hive-site.xml
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

��������libĿ¼������mysql������
wget http://central.maven.org/maven2/mysql/mysql-connector-java/5.1.41/mysql-connector-java-5.1.41.jar

�ġ���ʼ��Mysql���ݿ��
cd /usr/local/hive/bin
schematool-initSchema -dbType mysql
�����֡�schemaTool completed���������Ѿ���ʼ���ɹ�

�塢�鿴Mysql��hive���ݿ�
����57�ű�

��������hive
cd /usr/local/hive/bin
hive

�ߡ���Hive�д�����
cd /usr/local/hive
hive
show databases;
use default;
create table word_count(line string);
show tables;
select * from word_count;

�ˡ��۲�hadoop fs -ls /user��׼��HDFS��Ӧ�������ݣ��ϴ���/wcinput
hadoop fs -ls /
hadoop fs -mkdir /wcinput/
hadoop fs -put /usr/local/hadoop/hadoop_temp/3.txt /wcinput

�š�hive�������ݴ���
load data inpath '/wcinput' overwrite into table word_count;

ʮ��hiveִ����Ӧ�������鿴ִ�н����ִ�н�������´�����word_count_result�У�
select * from word_count;
select split(line, ' ') from word_count;
select explode(split(line, ' ')) from word_count;
select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word;
select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word order by count;
create table word_count_result as select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word order by count;
select * from word_count_result;

