1. **标准 Servlet Web 应用结构**

```
MyWebApp/
 ├─ index.html                  # 静态首页
 ├─ css/                        # 静态资源
 ├─ js/                         # 静态资源
 ├─ images/                     # 静态资源
 ├─ WEB-INF/                    # 受保护目录（不能直接通过 URL 访问）
 │    ├─ web.xml                # Web 应用部署描述符（Servlet 配置）
 │    ├─ classes/               # 编译后的 .class 文件（包结构保持一致）
 │    │    └─ com/example/...   # 你的 Java 类字节码
 │    └─ lib/                   # 依赖的 JAR 包
 └─ META-INF/
      └─ MANIFEST.MF            # 元信息（通常不用手改）

```

外部浏览器一般不能直接访问 `/WEB-INF` 下的文件，否则就会造成配置文件泄露，比如/WEB-INF/web.xml泄露

#### **/WEB-INF/**

- 用途：
  - `web.xml`：配置 Servlet、Filter、Listener、欢迎页、错误页等。
  - `classes/`：放编译后的应用类（按包名目录存放）。
  - `lib/`：放第三方依赖 JAR。

> 例如，你的 `com.example.HelloServlet` 编译后会在：

```
WEB-INF/classes/com/example/HelloServlet.class
```