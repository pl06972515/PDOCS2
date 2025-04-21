<br/>

>[!WARNING|style: flat|label: 简要说明 ]
>
>- (`FastAPI`) 是一个现代、快速高性能的`Web`框架，用于构建`API`[ 基于`Python 3.7+`的类型提示功能 ]
>
>   [`pip install fastapi[standard]`]
>
>   [<span style='color:#008B00'>[👓 官方首页 ]</span>](https://fastapi.tiangolo.com/zh/tutorial/#_1 ':target=_blank') [ 内置`Swagger UI - http://x.x.x.x/docs`]
>
>
><br/>

```csharp
from fastapi import FastAPI

app = FastAPI()

/*... Q: Requests ...*/
    
if __name__ == "__main__":
	import uvicorn
        
    // Main.py -> app 对象
	uvicorn.run("Main:app",
				host = "0.0.0.0", port = 8000,
				reload = True, // 生产环境: Flase
				workers= 4)
        
        
```

