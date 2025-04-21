<br/>

>[!WARNING|style: flat|label: 简要说明 ]
>
>- (`FastAPI`) 通过 <span style='color:red'>[ 类型声明 → 自动解析请求中的数据 ]</span>
>
>- <span style='color:red'>[ 请求标识：`Path( ... ), Query( ... ), Form( ... ), Body( ... ), Header( ... )`] [ 注意：其元数据配置等同于`Path( ... )` ]</span>
>
>- (`bool = True`) 解析支持`1, True, true, on, yes`
>
>- (`jsonable_encoder`) 将复杂的`Python`对象（`如 Pydantic 模型、ORM 模型等`）<span style='color:red'>[ 转换成`JSON`可序列化的字典`Dict`]</span>
>
> ```csharp
> from fastapi.encoders import jsonable_encoder
>
> class Item(BaseModel):
>      title: str
>      timestamp: datetime
>      description: str | None = None
>
>---------------------------------
>         
> @app.put("/M1")
> def update_item(item: Item):
>      dict = jsonable_encoder(item)
>
>
> ```
>
>
>
><br/>



<!-- tabs:start -->

#### **[ Route ]路由变量**

!> <span style='color:red'>[ 暂不支持：可选片段，无需显式声明`Path()`]</span>

```csharp
/// <summary>
/// GET http://localhost:5001/M1/10/1
///    - p1: int
///    - p2: 枚举 in (1, 2, 3) [ 注意：单个变量不支持 Literal[ 1, 2, 3 ] ]
///
/// </summary>
@app.get("/M1/{p1}/{p2}")
async def M1(p1: int, p2: Annotated[Color, Path(...)]):
	v =  p2 is Color.RED
	return {"key": f"Hello World: { p1 } { p2 }"}

-------------------------------------

from enum import Enum
class Color(int, Enum):
	RED = 1
	GREEN = 2
	BLUE = 3
        
        
```





#### **[ GET ]查询参数**

!> <span style='color:red'>[ 绑定复合对象，需显式声明`Query()`]</span>

```csharp
/// <summary>
/// GET http://localhost:5001/M1
///    - skip:  默认值 0
///    - limit: 默认值 10
///    - p1: in (int, None) 默认值 None ( 非 None 将限制长度 )
///    - p2: ?qs=foo&qs=bar
///
/// </summary>
@app.get("/M1")
async def M1(p1: Annotated[str, Query(..., min_length=10)],
			 p2: Annotated[List[str] | None, Query()] = None,
			 skip: int = 0,
			 limit: int = 10):
	 return {"key": f"Hello World: { p1 } { skip } { limit }"}


```

```csharp
from fastapi import FastAPI, Query
    
// GET http://localhost:5001/M1?p1=10&p3=a
@app.get("/M1")
async def M1(o: Annotated[QParams, Query(...)]):
	return {
		"p1": o.p1,
		"p2": o.p2,
		"p3": o.p3
	}
    
---------------------------------

class QParams(BaseModel):
	p1: int
	p2: int = 10
	p3: Literal["a", "b", "c"]
    model_config = {"extra": "forbid"}


```





#### **[ POST ]Form**

!> <span style='color:red'>[ 需预先安装`pip install python-multipart`，字段需显式声明`Form()`]</span>

```csharp
/// <summary>
///  POST http://localhost:5001/M1
///  content-type: application/x-www-form-urlencoded
///  
///  p1=10&p2=a
///    - p1: 必选字段
///    - p2: in ("a", "b", "c") 
///    - p3: 默认值 10
///
/// </summary>
@app.post("/M1")
async def M1(p1: Annotated[int, Form(...)],
			 p2: Annotated[Literal["a", "b", "c"], Form(...)],
			 p3: Annotated[int, Form()] = 10):
	return {
		"p1": p1,
		"p2": p2,
		"p3": p3
	}


```

```csharp
from fastapi import FastAPI, Form
    
/// <summary>
///  POST http://localhost:5001/M1
///  content-type: application/x-www-form-urlencoded
///   
///  p1=30&p2=20&p3=a
///
/// </summary>
@app.get("/M1")
async def M1(o: Annotated[QParams, Form(...)]):
	return {
		"p1": o.p1,
		"p2": o.p2,
		"p3": o.p3
	}
    
---------------------------------

class QParams(BaseModel):
	p1: int
	p2: int = 10
	p3: Literal["a", "b", "c"]
    model_config = {"extra": "forbid"}


```





#### **[ POST ]Body**

!> <span style='color:red'>[ 默认绑定：无需显示声明`Body()`]</span>

```csharp
from fastapi import FastAPI, Body
    
/// <summary>
///  POST http://localhost:5001/M1
///  content-type: application/json
///   
///  {
///      "p1": 10,
///      "p3": "a"
///  }
///
/// </summary>
@app.post("/M1")
async def root(o: QParams):  // o: QParams = Body(...)
	return {
		"p1": o.p1,
		"p2": o.p2,
		"p3": o.p3
	}

/// <summary>
///  POST http://localhost:5001/M1
///  content-type: application/json
///   
///  {
///       "o": {
///          "p1": 10,
///          "p3": "a"
///       }
///  }
///
///  embed=True: 期望请求体中的数据在一个特定的键中 [ 而不是直接作为根对象 ]
///
/// </summary>
@app.post("/M1")
async def root(o: Annotated[QParams, Body(embed=True)]):
	return {
		"p1": o.p1,
		"p2": o.p2,
		"p3": o.p3
	}

-------------------------------------
    
class QParams(BaseModel):
	p1: int
	p2: int = 10
	p3: Literal["a", "b", "c"]
    model_config = {"extra": "forbid"}


```



#### **[ Cookie]**

!> <span style='color:red'>[ 支持绑定模型 ]</span>

```csharp
from fastapi import Cookie, FastAPI
    
@app.get("/M1")
async def M1(ads_id: str | None = Cookie(None)):
    return {"ads_id": ads_id}


```



#### **[ Header ]请求报头**

!> <span style='color:red'>[ 默认情况下，`Header` 把参数名中的字符由下划线`_`替换连字符`-`来提取请求头 ] [ 支持绑定模型 ]</span>

```csharp
from typing import List
from fastapi import FastAPI, Header
    
/// <summary>
/// POST http://localhost:5001/M1
/// X-Token: foo, bar
///
/// </summary>
@app.post("/M1")
async def M1(x_token: List[str] = Header(...)):
	 return {"X-Token values": x_token}


```





<!-- tabs:end -->
