# 模块生成：在已有项目中按规范扩展

> 适用于「已经按 go-init 规范搭好骨架」的项目。每种新增需求都有标准落点和模板。

## 场景 1：新增一个数据表 + CRUD 接口

需求示例：「新增 tag 表，要支持增删改查」

### 步骤

1. **建表 SQL**：放 `migrations/<yyyymmdd>_add_tag.sql`，并执行到本地 DB。
2. **model**：`app/model/tagModel/tag.go`
   ```go
   package tagModel

   import (
       "<module>/global"
       "time"
   )

   type Tag struct {
       ID        int    `json:"id"`
       Name      string `json:"name"`
       Color     string `json:"color"`
       CreatedAt int64  `json:"created_at"`
       UpdatedAt int64  `json:"updated_at"`
   }

   func Add(d Tag) (Tag, error) {
       d.CreatedAt = time.Now().Unix()
       d.UpdatedAt = time.Now().Unix()
       err := global.Db.Model(&Tag{}).Create(&d).Error
       return d, err
   }
   func Get(where map[string]interface{}) (Tag, error) { /* ... */ }
   func Edit(where map[string]interface{}, data interface{}) error { /* ... */ }
   func Delete(where map[string]interface{}) error { /* ... */ }
   func Count(where map[string]interface{}) int    { /* ... */ }
   func GetList(where string, start, limit int) ([]Tag, int) { /* ... */ }
   func GetAllList(where string) []Tag { /* ... */ }
   ```
3. **service**（如有跨表逻辑）：`app/service/tagService/tag.go`
   ```go
   package tagService

   import "<module>/app/model/tagModel"

   func CreateTag(name, color string) (tagModel.Tag, error) {
       return tagModel.Add(tagModel.Tag{Name: name, Color: color})
   }
   ```
4. **controller**：`app/controller/admin/tag.go`（管理后台）
   ```go
   package admin

   import (
       "<module>/app/service/tagService"
       "<module>/util/app/response"

       "github.com/gin-gonic/gin"
   )

   type tagCreateReq struct {
       Name  string `json:"name" binding:"required"`
       Color string `json:"color"`
   }

   func TagCreate(c *gin.Context) {
       var req tagCreateReq
       if err := c.ShouldBindJSON(&req); err != nil {
           response.FailWithMsg(c, "ParamError", err.Error())
           return
       }
       tag, err := tagService.CreateTag(req.Name, req.Color)
       if err != nil {
           response.Fail(c, err.Error())
           return
       }
       response.Success(c, tag)
   }
   ```
5. **路由注册**：在 `router/v1/web.go`
   ```go
   adminApi.POST("/tag/create", admin.TagCreate)
   adminApi.GET("/tag/list", admin.TagList)
   ```

### Checklist

- [ ] migrations 加 SQL
- [ ] model 套路 7 函数
- [ ] service（仅当有跨层逻辑时才建）
- [ ] controller 仅做绑定+调用+响应
- [ ] 在 web.go / api.go 注册
- [ ] 接口文档同步

## 场景 2：新增一个 admin-only 接口

- controller 放 `app/controller/admin/`
- 路由组 `r.Group("/api/v1/admin")` 上 `middleware.AdminAuth()`

## 场景 3：新增一个一次性命令（手动跑）

需求示例：「写个命令把所有用户头像 URL 重新签名」

1. `app/console/commands/RefreshAvatarCommand.go`
   ```go
   package commands

   import "fmt"

   type RefreshAvatarCommand struct{}

   func (RefreshAvatarCommand) Main() {
       fmt.Println("[RefreshAvatar] start")
       // ... 调 service
       fmt.Println("[RefreshAvatar] done")
   }
   ```
2. 在 `app/console/kernel.go` 的 `Commands` 列表追加：
   ```go
   { Name: "refreshAvatar", Short: "刷新所有用户头像签名", RunI: &commands.RefreshAvatarCommand{} },
   ```
3. 运行：`./<binary> refreshAvatar`

## 场景 4：新增一个定时任务

- 命令行模板同上（实现 `Main()`）。
- 在 `Schedule()` 注册：
  ```go
  _ = c.AddFunc("0 0 3 * * *", func() {
      commands.RefreshAvatarCommand{}.Main()
  })
  ```
- 单实例部署提示。

## 场景 5：新增配置项

需求示例：「接入新的第三方短信」

1. `config/config.go` 加 struct：
   ```go
   type Sms struct {
       AccessKeyID     string `yaml:"access_key_id"`
       AccessKeySecret string `yaml:"access_key_secret"`
       SignName        string `yaml:"sign_name"`
   }
   // 加到 Configure 里
   ```
2. `example.config.yaml` 加示例值。
3. 业务包通过 `config.GlobalConfig.Sms.XXX` 读取。**不允许**业务包再读取 yaml 文件。

## 场景 6：新增枚举/错误码

`app/enums/userEnum/status.go`：

```go
package userEnum

const (
    StatusActive   = 1
    StatusDisabled = 2
)
```

业务错误码：

```go
package result

var (
    UserNotFound      = ErrorCode{Code: 10001, Message: "用户不存在"}
    UserAlreadyExists = ErrorCode{Code: 10002, Message: "用户已存在"}
)
```

controller 使用：

```go
response.FailWithErrorCode(c, result.UserNotFound)
```

## 场景 7：新增 Redis Key

加到 `app/enums/redisKey.go`：

```go
const RedisKeyAvatarSignedURL = "avatar:signed:%d"
```

业务包：

```go
key := fmt.Sprintf(enums.RedisKeyAvatarSignedURL, userID)
global.Redis.Set(key, url, time.Hour)
```

## 场景 8：新增中间件

业务级中间件（如限流、日志）放 `app/middleware/`；
路由级中间件（鉴权、CORS）放 `router/middleware/`。
通过 `r.Use(...)` 或 `group.Use(...)` 装载。

## 反模式（禁止）

- ❌ 在 controller 里直接 `global.Db.Where(...)` 查表。 → 必须经 model
- ❌ 在 model 里 `import service`。 → 循环依赖
- ❌ controller 直接 `c.JSON(...)` 自定义结构。 → 必须 `response.XXX`
- ❌ 散落 redis key 字符串、yaml 直接读。 → 集中到 enums/config
- ❌ 在 service 里再开一个 `*gorm.DB` 实例。 → 用 `global.Db`
- ❌ 在 cron 任务里依赖单进程内存状态。 → 多实例幂等
