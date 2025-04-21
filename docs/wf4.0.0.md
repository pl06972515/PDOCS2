<br/>

>[!WARNING|style: flat|label: 简要说明 ]
>
>- (`Depends`) 是`FastAPI`框架中的一个核心功能 <span style='color:red'>[ 用于实现依赖注入(`Dependency Injection`)，以下是它的主要作用和特点：]</span>
>
>  依赖注入：通过`Depends`，你可以将某些逻辑（如身份验证、数据库连接等）提取到单独的函数中<span style='color:red'> [ 并在路由处理函数中复用 ]</span>
>
>  简化代码：它帮助你避免重复代码，使主处理函数更加清晰和专注于核心逻辑
>
>  自动解析：(`FastAPI`) 会自动解析依赖关系树，并按需调用依赖
>
><br/>

<!-- tabs:start -->

#### **[ 函数依赖项 ]**

```csharp
from typing import Union, Annotated, Literal
from fastapi import Depends, FastAPI, Form
app = FastAPI()

// [ 创建依赖项 ] 依赖项就是一个函数 [ 可以使用与路径操作函数相同的参数 ]
async def M1(p1: Annotated[int, Form()],
             p2: Annotated[Literal["a", "b", "c"], Form()],
             p3: Annotated[int, Form()] = 10):
	return {"p1": p1, "p2": p2, "p3": p3}


```

```csharp
/// <summary>
/// POST http://localhost:8000/Q1
/// content - type: application / x - www - form - urlencoded
///
/// p1=10&p2=a
/// 
/// </summary>
@app.post("/Q1")
async def Q1(commons: Annotated[dict, Form(...), Depends(M1)]):
	return commons

/// <summary>
/// POST http://localhost:8000/Q2
/// content - type: application / x - www - form - urlencoded
///
/// p1=20&p2=c
/// 
/// </summary>
@app.post("/Q2")
async def Q2(commons: Annotated[dict, Form(...), Depends(M1)]):
	return commons
        
        
```



#### **[ ☢ 类型依赖项 ]**

!> <span style='color:red'>[ 推荐使用 ] 支持智能感知 [ 可以为属性指定不同的来源，根据参数注解从请求中提取数据 ]</span>

```csharp
// [ 无需继承 ] BaseModel ( FastAPI 内部自动处理 )
class QueryParams():
	def __init__(self,
				 p1: Annotated[int, Form()],
             	p2: Annotated[Literal["a", "b", "c"], Query()],
             	p3: Annotated[int, Header()] = 10):
		self.p1 = p1
		self.p2 = p2
		self.p3 = p3
            
            
```

```csharp
@app.post("/Q1")
async def Q1(commons: QueryParams = Depends()):
	return commons


@app.post("/Q2")
async def Q2(commons: Annotated[QueryParams, Depends()]):
	return commons
        
        
```



#### **[ ☢ yield ]IDisposable**

!> [<span style='color:#008B00'>[👓 上下文管理器 ]</span>](v1.0.0 ':target=_blank')

```csharp
@contextmanager
def DbContextManager(x: int, y: int):
	print("[ A ] 资源分配")
	try:
		yield f"[ 资源 - { x+y } ]"
	finally:
		print("[ C ] 资源释放")


```

```csharp
// FastAPI 内部对 yield 自动处理 [ 无需显示 @contextmanager ]
async def Get_DbContextManager():
	with DbContextManager(10, 20) as db:
		yield db

@app.get("/Q1")
async def Q1(commons: Annotated[str, Depends(Get_DbContextManager)]):
	return commons
        
        
```





<!-- tabs:end -->
