<br/>

<!-- tabs:start -->

#### **JSON**

!> <span style='color:red'>[ 默认情况下：迭代器类型`<class 'generator'>`对象自动转换`JSONResponse`将其序列化为`JSON`格式 ]</span>

```csharp
@app.get("/M1", response_model=Dict[str, float])
async def M1():
    return {"foo": 2.3, "bar": 3.4}


```



```csharp
/// <summary>
/// [ 控制响应模型 ] 
/// - response_model_exclude_none : 如果某个字段的值是 None [ 它将不会出现在返回结果中 ]
/// - response_model_exclude_defaults: 排除值等于默认值的字段 ( 即使字段被显式赋值, 但如果其值等于默认值,也会被排除 )
/// - response_model_exclude_unset: 排除未显式赋值的字段 ( 只返回那些在实例化模型时被显式赋值的字段 )
/// 
/// </summary>
@app.get("/M1", response_model=User, response_model_exclude_defaults=True)
async def M1() -> Any:
	return User(username="test", password="372577325@qq.com")

@app.get("/M2", response_model=List[User], response_model_exclude_defaults=True)
async def M2() -> Any:
	return [
         User(username="test1", password="372577325@qq.com"),
         User(username="test2", password="372577325@qq.com")
    ]
        
----------------------------------
    
class User(BaseModel):
	username: str
	password: str
	full_name: str | None = None
        
        
```



#### **[ 文本 ]Text**

!> <span style='color:red'>[ 响应对象`PlainTextResponse : 'text/plain; charset=utf-8'`]</span>

```csharp
from starlette.responses import PlainTextResponse

@app.get("/M1", response_class = PlainTextResponse)
async def M1() -> Any:
	// return PlainTextResponse("你好，张三", status_code=200, media_type = "text/plain; charset=utf-8")
	return "你好，张三"


```



#### **StatusCode**

```csharp
from starlette import status
from starlette.responses import PlainTextResponse
    
@app.get("/M1", response_class = PlainTextResponse, status_code=status.HTTP_200_OK)
async def M1() -> Any:
	return "你好，张三"
        

```



#### **[ ☢ 异常状态 ]HTTPException**

```csharp
from starlette.exceptions import HTTPException
    
@app.get("/M1")
async def M1(x: int = 2) -> Any:
	if x == 1:
		return {"x": 1}
	if x == 2:
		
        // [ 引发错误异常 ] 返回 JSON { "detail": "x > 10" }
        // detail: 支持 dict、list 等数据结构 FastAPI 能自动处理这些数据，并将之转换为 JSON
        raise HTTPException(status_code=500, detail="x > 10")
	return items


```



#### **[ ☢ 元数据 ]Swagger**

```cs
class Item(BaseModel):
	Name: str
	Age: int = Field(..., title="年龄",description="字段描述")

	// [提供案例]
	model_config = {
		"json_schema_extra": {
			"examples": [
				{
					"name": "张三",
					"Age": 30
				}
			]
		}
	}

@app.post(
	"/items/",
	response_model=Item,
	summary="数据新增",
	response_description="数据返回",
    deprecated=True  // 是否弃用
)
async def create_item(item: Item = Body(...)):
	"""
	数据新增:

	- **name**: 姓名
	- **Age**: 年龄
	"""
	return item

        
```



<!-- tabs:end -->
