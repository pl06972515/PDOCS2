<br/>

>[!WARNING|style: flat|label: 简要说明 ]
>
>- (`.html`) 用于测试`HTT`的请求文件 ( 通常以`.http`为扩展名 ) [<span style='color:#008B00'>[👓 官方说明 ]</span>](https://learn.microsoft.com/zh-cn/aspnet/core/test/http-files?view=aspnetcore-9.0#http-file-syntax ':target=_blank')
>
>```http
># 1.以 # 开头的行是 [ 注释 ]
># 2.用户变量
>    - 以 @ 开头定义变量 @VariableName=Value 
>    - 通过 {{ @VariableName }} 引用变量
>
># 3.引用请求: {{<request name>.(response|request).(body|headers).(*|JSONPath|XPath|<header name>)}}
>     > {% client.global.set("key", response.body.key); %} JSONPATH 不需要 $
> 
> 
> ```
>
>
>
><br/>



<!-- tabs:start -->

#### **GET**

```http
@host = http://localhost:5230

### M1 : 测试请求
GET {{host}}/M1?id=1

> {% client.global.set("key", response.body.key); %}


```

```http
@host = http://localhost:5230

### M2 : 测试请求
GET {{host}}/M2?key={{key}}


```



#### **[ POST ] From**

```http
### POST (application/x-www-form-urlencoded)
POST {{host}}/M1
Content-Type: application/x-www-form-urlencoded

file1 = John &
file2 = 30


```



#### **[ POST ] JSON**

```http
### POST (application/json)
POST {{host}}/M1
Content-Type: application/json

{
    "date": "2023-05-10",
    "temperatureC": 30,
    "summary": "Warm"
}


```



<!-- tabs:end -->
