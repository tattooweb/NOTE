# diva

- drozer要在drozer目录下cmd启动(python目录)

- > adb devices
  >
  > adb connect 127.0.0.1:62001
  >
  > adb forward tcp:31415 tcp:31415
  >
  > drozer console connect

- 各种模块报错：卸载+重新安装低版本
- drozer报错10056
- drozer使用run的时候报错

### 1. INSECURE LOGGING

app代码中有敏感信息输出

adb shell

logcat xxx

### 2. HARDCODING ISSUES - PART 1

硬编码，寻找java代码中是否存在硬编码密钥

### 3. INSECURE DATA STORAGE - PART 1

不安全的数据存储：将敏感数据保存到配置文件中

位置：/data/data/(package name)/shared_prefs/xxx.xml

### 4. INSECURE DATA STORAGE - PART 2

不安全的数据存储：将敏感数据保存到本地数据库中

位置：/data/data/(package name)/databases

使用adb的sqlite数据库

sqlite3 <库名>

.tables

select  * from table_name;

### 5. INSECURE DATA STORAGE - PART 3

不安全的数据存储：临时数据

位置：/data/data/(package name)/xxxxtmp

cat xxxtmp

### 6. INSECURE DATA STORAGE - PART 4

不安全的数据存储：外部sdcard

位置：/mnt/sdcard/xx

### 7. INPUT VALIDATION ISSUES - PART 1

输入非预期事件，SQL注入等

### 8. INPUT VALIDATION ISSUES - PART 2

输入非预期事件，未限制协议，任意读文件等

### 9. ACCESS CONTROL ISSUES - PART 1

```
adb shell am start jakhar.aseem.diva/.APICredsActivity

adb shell am start -n jakhar.aseem.diva/.APICredsActivity -a jakhar.aseem.diva.action.VIEW_CREDS
```

### 10. ACCESS CONTROL ISSUES - PART 2

### 11.ACCESS CONTROL ISSUES - PART 3

### 12. HARDCODING ISSUES - PART 2

### 13. INPUT VALIDATION ISSUES - PART 3

# 公共组件漏洞

### Activity

```bash
//调用暴露的activity组件
run app.activity.info -a packagename
run app.activity.start --component packagename activityname
```

### Broadcast Receiver

```bash
//是否存在信息泄露
run app.broadcast.info -a packagename
代码审计
//拒绝服务攻击
run app.broadcast.send --component packagename action_name
```

### Service

```bash
run app.service.info -a packagename
run app.service.start --component 包名 服务名
```

### Content Provider

```bash
//获取content provider组件信息
run app.provider.info -a packagename

//检测URI数据泄露风险
//获取内容提供器的 URIS 资源访问路径
run scanner.provider.finduris -a packagename

//敏感数据读取
run app.provider.query uri
run app.provider.query content://packagename.DBContentProvider/Passwords

//检测是否存在 SQL 注入
run scanner.provider.injection -a packagename
run app.provider.query contentProviderURI --selection "'"

//检测是否存在目录遍历
run scanner.provider.traversal -a packagename

//读取系统文件
run app.provider.read content://packagename.FileBackupProvider/etc/hosts
```

### 开发者证书

```bash
cd /META-INF
keytool -printcert -file *.RSA
```

### PULL

```bash
mkdir data
adb pull /data/data/packagename ./
```
