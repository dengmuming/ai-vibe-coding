---
name: go-init
description: 基于 gin + gorm + redis + zap + mix-go/xcli 的 Go 后端项目脚手架规范（源自 mindcloudxai_api 的实战架构）。Claude 应在用户提到 "初始化 Go 项目"、"创建 Go 后端服务"、"搭建 gin 项目"、"新建 Go API 项目"、"按 mindcloudxai 风格搭建项目"，或在现有此风格 Go 项目中"新增控制器/服务/模型/路由/计划任务命令/枚举/中间件"时使用本 skill，即使用户没有显式说"脚手架"或"模板"。也用于把已有的散乱 Go 代码改造成本规范的分层结构。
---

# go-init

为 Go 后端项目（HTTP API + 计划任务 + 命令行）提供一套统一的目录结构与组件接入规范。包含：从零搭建新项目，以及在已有项目中按规范扩展。

## 快速判断：用户在做什么？

| 用户意图 | 走哪条路径 |
|---|---|
| 新建一个空项目 / `go mod init` 后不知怎么组织 | **路径 A：新项目初始化** |
| 已有项目，要加一个接口 / 表 / 命令 | **路径 B：按规范扩展模块** |
| 散乱代码改造 | 先按路径 A 立骨架，再迁移代码进对应分层 |

不确定时主动询问用户：模块名（`go.mod` 的 module path）？需要哪些可选组件？

## 路径 A：新项目初始化

### A1. 目录骨架（必建）

```
<project>/
├── app/
│   ├── connection/{mysql,redis}/
│   ├── controller/        # 按域分子包，如 user/、project/
│   ├── service/           # 按域分子包，命名 xxxService
│   ├── model/             # 按域分子包，命名 xxxModel
│   ├── enums/{result}/
│   ├── middleware/
│   └── console/
│       ├── kernel.go
│       └── commands/
├── router/
│   ├── router.go
│   ├── middleware/        # auth.go, public.go(cors)
│   └── v1/                # web.go / api.go
├── config/config.go
├── global/global.go
├── util/{logger,i18nutil,app/response,common}/
├── lang/                  # zh.toml, en.toml（若启用 i18n）
├── migrations/            # SQL 迁移
├── static/  public/  log/  output/
├── main.go
├── example.config.yaml
├── .gitignore
├── build.sh
├── run.bat
└── go.mod
```

> 一次性建目录命令（PowerShell，逐行确认；如用 bash 用 `mkdir -p`）：
> `New-Item -ItemType Directory -Force -Path app/connection/mysql,app/connection/redis,app/controller,app/service,app/model,app/enums/result,app/middleware,app/console/commands,router/middleware,router/v1,config,global,util/logger,util/i18nutil,util/app/response,util/common,lang,migrations,static,public,log,output`

### A2. 必装核心组件（按顺序接入）

> 以下每一步的**完整代码模板**见 `references/`，本节只列接入顺序与关键点。

1. **`config/config.go`** — yaml 配置加载（`gopkg.in/yaml.v2`），暴露 `config.GlobalConfig` 单例与 `InitConfig()`。
   → 见 `references/components-logger-config.md`
2. **`global/global.go`** — 声明全局单例：`Db *gorm.DB`、`Redis *MindCloudRedis`、`Logger *zap.Logger`、`RestyClient *resty.Client`。**禁止在任何业务包里再定义全局**。
3. **`util/logger/logger.go`** — `zap` + `lumberjack` 的滚动日志，输出到 `./log/log.log` 与 stdout。
   → 见 `references/components-logger-config.md`
4. **`app/connection/mysql/mysql.go`** — `gorm.Open` 单例工厂 `GetDbClient()`，使用单数表名 `SingularTable: true`。
   → 见 `references/components-mysql-redis.md`
5. **`app/connection/redis/redis.go`** — 自封装 `MindCloudRedis`（暴露 `Get/Set/LPush/RPop/...`），返回 `*MindCloudRedis`。
   → 见 `references/components-mysql-redis.md`
6. **`router/router.go`** — `InitRouter() *gin.Engine`：装 i18n bundle + `Cors` 中间件 + 静态资源 + `v1.InitWebRouter`。
   → 见 `references/components-router-i18n.md`
7. **`router/middleware/`** — `public.go`（CORS）、`auth.go`（`TokenAuth`/`AdminAuth`）。
8. **`util/app/response/response.go`** — 统一响应：`Success/Fail/TokenFail/PermissionDenied/FailWithErrorCode`，配合 `app/enums/result.ErrorCode`。
9. **`app/console/kernel.go`** — `xcli.Command` 列表，默认 `run` 命令调起 HTTP 服务；`Schedule()` 用 `robfig/cron` 跑定时任务。
   → 见 `references/components-xcli-cron.md`
10. **`main.go`** — `init()` 调 `initConfig()` 装填全局；`main()` 解析 `--host` `--schedule` flag，否则交给 `xcli.AddCommand(...).Run()`。
    → 完整模板见 `references/components-xcli-cron.md`

### A3. 可选组件（按需问用户）

- **i18n**：`lang/zh.toml` `lang/en.toml` + `suisrc/gin-i18n` + `util/i18nutil`
- **阿里云 OSS / 腾讯云 COS / STS**：见 `references/components-storage.md`
- **校验器多语言报错**：`go-playground/validator/v10` + `universal-translator` → `util/app/validator.go`
- **Excel 导出**：`360EntSecGroup-Skylar/excelize` → `util/office`
- **Resty HTTP 客户端**：`go-resty/resty/v2`（核心 `global.RestyClient`）

### A4. 配置与构建文件

- **`example.config.yaml`** — 配置模板（提交进库），项目实际配置 `config.yaml` **必须 .gitignore**。结构与 `config/config.go` 的 struct tag 完全对齐。
- **`.gitignore`** 至少包含：
  ```
  *.exe
  *.exe~
  *.test
  *.out
  .idea/*
  config.yaml
  main
  main.exe
  build/*
  tmp/*
  log/*
  output/*
  .gocache
  ```
- **`build.sh`**：
  ```bash
  #!/bin/bash
  source /etc/profile
  go mod tidy
  CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o "<binary_name>" main.go
  ```
- **`run.bat`**：本地 Windows 启动脚本（可选）

### A5. 收尾

- `go mod init <module>` → 让用户给出 module 名（不要瞎猜）
- `go mod tidy` 拉依赖
- 提示用户：复制 `example.config.yaml` 为 `config.yaml` 并填值，然后 `go run main.go`
- 计划任务模式：`go run main.go --schedule=true`

## 路径 B：在现有项目中按规范扩展

> 触发关键词：「加一个 XX 接口」「新增 XX 表」「写个定时任务跑 XX」「加个管理后台 XX 功能」
> 详细的逐场景模板见 `references/module-generation.md`。这里给最小决策树：

| 要做的事 | 落点 |
|---|---|
| 新增一个数据表的 CRUD | `app/model/<domain>Model/<file>.go` 提供 `Add/Get/Edit/Delete/Count/GetList/GetAllList` |
| 新增一个业务流程（跨多表 / 调用 OSS / 算指标） | `app/service/<domain>Service/<file>.go` |
| 新增一个 HTTP 接口 | `app/controller/<domain>/<file>.go` 写 handler；在 `router/v1/web.go`（或 `api.go`）注册 |
| 新增一个 admin-only 接口 | controller 放 `app/controller/admin/`；路由组用 `middleware.AdminAuth()` |
| 新增一个一次性脚本 / 定时任务 | `app/console/commands/XxxCommand.go` 实现 `Main()`；在 `app/console/kernel.go` 的 `Commands` 列表注册；如需 cron，在 `Schedule()` 中 `c.AddFunc` |
| 新增配置项 | 必须先在 `config/config.go` 的 struct 加字段，再在 `example.config.yaml` 加示例值 |
| 新增枚举/常量 | `app/enums/<domain>Enum/`；错误码定义为 `result.ErrorCode{Code, Message}` |
| 新增 Redis key | 集中到 `app/enums/redisKey.go`，禁止散落字符串 |

## 必须遵守的代码约束

1. **分层不可越界**：
   - `controller` 只做 参数绑定/校验/调 service/返回 response，**禁止**直接 import `model` 写库。
   - `service` 持有业务逻辑，可调用多个 `model`、`util`，**禁止** import `controller` / `router`。
   - `model` 仅 GORM 结构体 + 基础 CRUD，**禁止** import `service`。
2. **全局单例只在 `global` 包**：业务包通过 `global.Db` / `global.Redis` / `global.Logger` 访问，**禁止**在 controller/service 里二次实例化。
3. **包命名**：小写、无下划线；按域分包；service 包后缀 `Service`，model 包后缀 `Model`，enum 包后缀 `Enum`。
4. **响应统一**：所有 controller 必须用 `util/app/response` 的方法，禁止直接 `c.JSON`。
5. **错误码**：所有业务错误码在 `app/enums/result/` 集中声明为 `ErrorCode` 常量，由 `response.FailWithErrorCode` 返回。
6. **配置先行**：新增任何外部依赖配置，**先**改 `config/config.go` 的 struct，**再**改 `example.config.yaml`，**再**写业务代码。
7. **计划任务幂等**：`Schedule()` 中注册的 cron 任务必须考虑重复触发（项目已踩过队列多实例的坑）；多实例部署时只在一台跑。
8. **风格**：保持现有项目的注释格式（如 `// @Description`、`// MakeList ...`），不要混用其它风格。

## 运行约定

- HTTP 服务（默认）：`go run main.go` 或 `./<binary>`，端口由 `config.yaml` 的 `system.host` 或 `--host` flag 决定。
- 计划任务：`go run main.go --schedule=true`，**与 HTTP 服务分进程部署**。
- 一次性命令：`./<binary> <command_name>`，例如 `./mindcloudxai_api refreshTaskCount`。

## 沟通风格提示

- 用户给的需求若缺关键信息（module 名、是否要 OSS、是否多语言），**主动一问**，不要瞎填占位符。
- 大段代码生成前，先给用户列「将创建的文件清单」让其确认，避免一次性 dump 过多。
- 引用代码模板时优先 read `references/` 对应文件，再按当前用户场景裁剪输出。
