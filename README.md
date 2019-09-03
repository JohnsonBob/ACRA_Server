## ACRA Server
Docker Image of CouchDB with acra-storage & acralyzer

### ACRA简介
[ACRA 官网](https://github.com/ACRA/acra "ACRA 官网")

> ACRA(Application Crash Reporting on Android)，Android应用程序崩溃上报系统。在客户端集成ACRA的SDK，便可将应用程序的崩溃错误上报到服务端。ACRA内部实现了崩溃上报的策略，客户端采用Annotation的方式进行代码集成，对已有的业务代码影响非常小。

### HOW USE
``` shell
git clone git@github.com:JohnsonBob/ACRA_Server.git
docker-compose up -d
```

> CouchDB Manage Address http://127.0.0.1:5984/_utils

> Acralyzer Manage Address http://127.0.0.1:5984/acralyzer/_design/acralyzer/index.html


### docker-compose.yml
``` yml
version: "3"

networks: 
  ghost: 

services: 
  acralyzer: 
    image: "couchdb:2.3.1"
    restart: always
    networks: 
      - ghost
    environment: 
      COUCHDB_USER: admin
      COUCHDB_PASSWORD: password
    volumes: 
      - $PWD/data:/opt/couchdb/data
    ports: 
      - "5984:5984"
```

### Use Android
#### 1、首先 Project build.gradle中加入
```
buildscript {
    ext.acra_version = '5.4.0'
}
```
#### 2、app build.gradle中加入
```
dependencies {
    //acra-http
    implementation "ch.acra:acra-http:$acra_version"
}
```

#### 3、在Acralyzer Manage中Admin->Users中添加用户和密码acra/acra

#### 4、在MyApplication中初始化
```
@AcraHttpSender(
    uri = "http://IP Or Domain Name:5984/acra-storage/_design/acra-storage/_update/report",
    httpMethod = HttpSender.Method.PUT,
    basicAuthLogin = "acra",
    basicAuthPassword = "acra"
)
class MyApplication : Application() {
    
    override fun attachBaseContext(base: Context?) {
        super.attachBaseContext(base)

        //初始化ACRA
        ACRA.init(this)
        ACRA.getErrorReporter().handleSilentException(Exception("I am exception."))
    }
}

```