# mesapi
mes message parse and process logic managerment

mesapi/
├── .vscode/             # 调试配置
│   ├── launch.json      # 配置 VS Code 调试器
├── app/                 # 核心应用
│   ├── __init__.py
│   ├── main.py          # 入口文件
│   ├── routers/         # 路由拆分
│   │   ├── __init__.py
│   │   ├── msg.py       # 消息处理相关路由
│   │   └── rules.py     # 业务相关路由
│   ├── msgparser/       # 消息解析器
│   │   ├── __init__.py
│   │   └── *.py         # 消息结构解析程序
│   ├── msgstruct/       # 消息结构定义
│   │   ├── __init__.py
│   │   └── *.xls        # 消息结构定义文件
│   ├── dependencies.py  # 依赖项（如认证、数据库连接）
│   └── telegram.json    # 全局类名字典
├── requirements.txt     # 依赖清单
└── .env                 # 环境变量（多数据库配置，Redis配置）

项目说明：
1、使用fastapi建立一个接收字符串电文并实时解析内容，转换为结构化数据，进行逻辑处理，并存入数据库的服务；
2、由于数据量较大，为了不堵塞，需要使用FIFO模式的消息队列；
3、由于电文格式较多，每个电文需建立一个解析类，解析类需可以动态加载，服务上线后随时可以修改解析类，修改完后热加载；
4、服务可根据电文的特征值，调用对应的解析类；
5、电文结构化处理后的数据处理逻辑需可维护，为了加快访问速度，数据处理逻辑需放到Redis中；
6、项目需要能随时监控接收的电文内容与解析的情况，能查看已加载的解析类、数据处理逻辑内容。

项目依赖虚拟环境：
    source /home/jupyterhub-env/bin/activate

FastAPI 最大优势是自动校验、自动生成文档、高性能，结合 VS Code 的调试能力，能高效开发接口。
1. 启动服务
    uvicorn main:app --reload

    生产环境建议直接用命令行启动
    uvicorn main:app --host 0.0.0.0 --port 80 --workers 4

    systemctl启动
    systemctl daemon-reload
    systemctl start mesapi
    systemctl enable mesapi

    查看服务日志
    journalctl -u mesapi -f

2. 测试接口
    直接访问：浏览器打开 http://127.0.0.1:8000，返回 {"message":"Hello FastAPI!"}；
    带参数访问：http://127.0.0.1:8000/items/10?q=test，返回 {"item_id":10,"q":"test"}；
    自动生成的接口文档（FastAPI 核心优势）：
        Swagger UI：http://127.0.0.1:8000/docs（可直接在线测试接口）；
        ReDoc：http://127.0.0.1:8000/redoc（更简洁的文档风格）。

