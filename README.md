#### 一、简介

cmdp-server是cmdp工具的服务端，如果希望自己运行服务端，启动cmdp-server即可。



#### 二、启动步骤

##### 1. 下载对应平台的cmdp-server

[mac](https://github.com/yurencloud/cmdp-server-public/releases/download/v3.0.0/cmdp-server-mac.tar.gz) | [windows](https://github.com/yurencloud/cmdp-server-public/releases/download/v3.0.0/cmdp-server-windows.tar.gz) | [linux](https://github.com/yurencloud/cmdp-server-public/releases/download/v3.0.0/cmdp-server-linux.tar.gz)

##### 2. 安装mysql和redis

在mysql中创建cmdp数据库

##### 3. 修改 `conf/app.conf`配置

连接你的mysql和redis

```
[dev]
httpport = 8080
mysqluser = root
mysqlpass = secret
mysqlurls = localhost:3306
mysqldb   = cmdp
jwtkey = 123456
redis = {"key":"cmdp","conn":"127.0.0.1:6379","dbNum":"0","password":"secret"}
```

##### 4. 启动服务端

```
chmod +x ./cmdp-server
./cmdp-server
```



#### 三、接口文档

##### 注册

 `请求: POST `  `参数类型: PATH` `URL: http://localhost:8080/v1/user/register` 

| 名称     | 参数名   | 类型 | 默认值 | 备注 |
| -------- | -------- | ---- | ------ | ---- |
| 用户名   | Username | POST | /      |      |
| 登录密码 | Password | POST | /      |      |



##### 登录

 `请求: POST `  `参数类型: PATH` `URL: http://localhost:8080/v1/user/login` 

| 名称     | 参数名   | 类型   | 默认值 | 备注 |
| -------- | -------- | ------ | ------ | ---- |
| 用户名   | Username | String | /      |      |
| 登录密码 | Password | String | /      |      |



##### 创建命令

 `请求: POST `  `参数类型: PATH` `URL: http://localhost:8080/v1/cmdp/create` 

| 名称     | 参数名  | 类型    | 默认值 | 备注          |
| -------- | ------- | ------- | ------ | ------------- |
| 命令内容 | Cmd     | String  | /      |               |
| 关键词   | Keyword | String  | /      |               |
| 注释     | Comment | String  | /      |               |
| 是否私有 | Private | Boolean | 1      | 0 公开 1 私有 |





```

func Create(cmd Cmd) Respond {
	result := Respond{}
	cmdJson, _ := json.Marshal(cmd)
	body := HttpJson("POST", "/cmdp/create", cmdJson)
	json.Unmarshal(body, &result)
	return result
}

func ForkCmd(keyword string) Respond {
	result := Respond{}
	body := Http("POST", "/cmdp/fork", "keyword="+keyword)
	json.Unmarshal(body, &result)
	return result
}

func Search(keyword string, page int, size int) CmdsRespond {
	result := CmdsRespond{}
	body := Http("POST", "/cmdp/search", "keyword="+keyword+"&page="+strconv.Itoa(page)+"&size="+strconv.Itoa(size))
	json.Unmarshal(body, &result)
	return result
}

func GetCmd(keyword string) CmdRespond {
	result := CmdRespond{}
	body := Http("POST", "/cmdp/getCmd", "keyword="+keyword)
	json.Unmarshal(body, &result)
	return result
}

func Delete(id string) Respond {
	result := Respond{}
	body := Http("GET", "/cmdp/delete/"+id, "")
	json.Unmarshal(body, &result)
	return result
}

// file

func CreateFile(file *File) Respond {
	result := Respond{}
	fileJson, _ := json.Marshal(file)
	body := HttpJson("POST", "/file/create", fileJson)
	json.Unmarshal(body, &result)
	return result
}

func ForkFile(keyword string) Respond {
	result := Respond{}
	body := Http("POST", "/file/fork", "keyword="+keyword)
	json.Unmarshal(body, &result)
	return result
}

func SearchFile(keyword string, page int, size int) FilesRespond {
	result := FilesRespond{}
	body := Http("POST", "/file/search", "keyword="+keyword+"&page="+strconv.Itoa(page)+"&size="+strconv.Itoa(size))
	json.Unmarshal(body, &result)
	return result
}

func DownloadFile(keyword string) FilesDownloadRespond {
	result := FilesDownloadRespond{}
	body := Http("POST", "/file/download", "keyword="+keyword)
	json.Unmarshal(body, &result)
	return result
}

func DeleteFile(id string) Respond {
	result := Respond{}
	body := Http("GET", "/file/delete/"+id, "")
	json.Unmarshal(body, &result)
	return result
}

// user

func Login(username string, password string) Respond {
	result := Respond{}
	body := Http("POST", "/user/login", "Username="+username+"&Password="+password)
	json.Unmarshal(body, &result)
	return result
}

func Register(username string, password string) Respond {
	result := Respond{}
	body := Http("POST", "/user/register", "Username="+username+"&Password="+password)
	json.Unmarshal(body, &result)
	return result
}

func ResetPassword(password string) Respond {
	result := Respond{}
	body := Http("POST", "/user/reset", "password="+password)
	json.Unmarshal(body, &result)
	return result
}

func UpdateInfo(info string) Respond {
	result := Respond{}
	body := Http("POST", "/user/info", "info="+info)
	json.Unmarshal(body, &result)
	return result
}

// star

type StarUser struct {
	StarId    int
	UserId    int
	Username  string    `orm:"size(255)"`
	CreatedAt time.Time `orm:"auto_now_add;type(datetime)"`
	CmdCount  int
	StarCount int
	FileCount int
	User      User
}

func CreateStar(username string) Respond {
	result := Respond{}
	body := Http("POST", "/star/create", "Username="+username)
	json.Unmarshal(body, &result)
	return result
}

func DeleteStar(id int) CmdsRespond {
	result := CmdsRespond{}
	body := Http("GET", "/star/delete/"+strconv.Itoa(id), "")
	json.Unmarshal(body, &result)
	return result
}

func SearchStar(page int, size int, keyword string) StarsRespond {
	result := StarsRespond{}
	body := Http("POST", "/star/search", "page="+strconv.Itoa(page)+"&size="+strconv.Itoa(size)+"&keyword="+keyword)
	json.Unmarshal(body, &result)
	return result
}

func SearchUser(page int, size int, keyword string, official string) UsersRespond {
	result := UsersRespond{}
	body := Http("POST", "/user/search", "page="+strconv.Itoa(page)+"&size="+strconv.Itoa(size)+"&keyword="+keyword+"&official="+official)
	json.Unmarshal(body, &result)
	return result
}

```

