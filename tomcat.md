# Tomcat集群

## Tomcat初步集群

对于用户来说

1不知道登录哪个服务器(负载均衡nginx)

2如果用户登录了一个服务器，那么如果访问另外一个服务器，是否还需要登录Session是否共享?

![](tomcat初步集群.png)

### 初步集群

#### 1在集群之前必须配置jdk

​	1.1解压jdk

​	1.2配置*vim /etc/profile*

​			按i进入编辑，在profile文件尾部添加如下内容

```

export JAVA_HOME=/usr/local/jdk1.8.0_181  #jdk安装目录
 
export JRE_HOME=${JAVA_HOME}/jre
 
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
 
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
 
export PATH=$PATH:${JAVA_PATH}

```

​	1.3*source /etc/profile* 使配置立刻生效

#### 2配置集群tomcat

​	1解压一个个tomcat,另外一个我们在cp一个tomcat

​	 2配置tomcat/tomcat2中的server.xml。这里示例tomcat2

```
/usr/local/tomcat2/conf/server.xml

```

在connector 中配置URIEncoding="UTF-8"

```xml
 <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIENcoding="UTF-8" />

```

#### 3配置端口

1改变server port

​	变成9005

```xml
<Server port="9005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
```

2 port变成9080其他不要动

```xml
<Connector port="9080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIENcoding="UTF-8" />

```

3改变AJP中的端口我们也变成9009,tomcat9，8默认是注释了

```xml
 <Connector protocol="AJP/1.3"
               address="::1"
               port="9009"
               redirectPort="8443" />

```



#### 4启动tomcat看是否成功

```
curl http://192.168.6.128:8080 
```

如果有html就说明本地启动成功。

#### 5坑

​		1本地启动成功，但是外边访问不了

​			多半都是防火墙惹的火，解决方法，要么关闭防火墙，要们在防火墙中配置8080端口

```
systemctl status firewalld.service
```

​	关闭防火墙

```
systemctl stop firewalld.service
```

禁止防火墙重启启动

```
systemctl disable firewalld.service 
```

