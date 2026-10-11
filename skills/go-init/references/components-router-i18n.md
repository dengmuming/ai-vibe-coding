# 组件：Router（gin） + i18n + 统一响应

## router/router.go

```go
package router

import (
	"<module>/router/middleware"
	v1 "<module>/router/v1"

	"github.com/gin-gonic/gin"
	i18n "github.com/suisrc/gin-i18n"
	"golang.org/x/text/language"
)

func InitRouter() *gin.Engine {
	r := gin.Default()

	// 国际化
	bundle := i18n.NewBundle(
		language.English,
		"lang/zh.toml",
		"lang/en.toml",
	)
	r.Use(i18n.Serve(bundle))

	// CORS
	r.Use(middleware.Cors())

	// 静态资源
	r.StaticFile("/", "./static/index.html")
	r.Static("/file", "./file")
	r.Static("/public", "./public")
	r.Static("/static", "./static")

	// 注册业务路由
	v1.InitWebRouter(r)
	return r
}
```

## router/middleware/public.go（CORS）

```go
package middleware

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

func Cors() gin.HandlerFunc {
	return func(c *gin.Context) {
		c.Header("Access-Control-Allow-Origin", "*")
		c.Header("Access-Control-Allow-Headers", "Content-Type,AccessToken,X-CSRF-Token,Authorization,token,platform")
		c.Header("Access-Control-Allow-Methods", "POST, GET, OPTIONS")
		c.Header("Access-Control-Expose-Headers", "Content-Length,Access-Control-Allow-Origin,Access-Control-Allow-Headers,Content-Type")
		c.Header("Access-Control-Allow-Credentials", "true")
		if c.Request.Method == "OPTIONS" {
			c.AbortWithStatus(http.StatusNoContent)
		}
		c.Next()
	}
}
```

## router/middleware/auth.go（鉴权）

```go
package middleware

import (
	"<module>/util/app/response"
	util "<module>/util/common"

	"github.com/gin-gonic/gin"
)

func TokenAuth() gin.HandlerFunc {
	return func(c *gin.Context) {
		token := getToken(c, "token")
		userInfo, err := util.GetUserInfo(token)
		if userInfo.Id == 0 || err != nil {
			response.TokenFail(c, nil)
			c.Abort()
			return
		}
		c.Set("userInfo", userInfo)
		c.Next()
	}
}

func AdminAuth() gin.HandlerFunc {
	return func(c *gin.Context) {
		token := getToken(c, "token")
		userInfo, err := util.GetUserInfo(token)
		if userInfo.Id == 0 || err != nil {
			response.TokenFail(c, nil)
			c.Abort()
			return
		}
		if !util.IsAdmin(userInfo) {
			response.PermissionDenied(c, nil)
			c.Abort()
			return
		}
		c.Set("userInfo", userInfo)
		c.Next()
	}
}

func getToken(c *gin.Context, key string) string {
	if v := c.GetHeader(key); v != "" {
		return v
	}
	return c.Query(key)
}
```

> `util.GetUserInfo` / `util.IsAdmin` / `util.CurrentUser` 由项目自行实现（一般在 `util/common`），可以是远端 API 校验或本地 JWT 解析。

## router/v1/web.go（路由表）

```go
package v1

import (
	"<module>/app/controller/admin"
	"<module>/app/controller/user"
	"<module>/router/middleware"

	"github.com/gin-gonic/gin"
)

func InitWebRouter(r *gin.Engine) {
	api := r.Group("/api/v1")

	// 公开接口
	{
		api.GET("/ping", func(c *gin.Context) { c.JSON(200, gin.H{"ok": true}) })
	}

	// 鉴权接口
	api.Use(middleware.TokenAuth())
	{
		api.POST("/user/info", user.Info)
	}

	// 管理后台
	adminApi := r.Group("/api/v1/admin")
	adminApi.Use(middleware.AdminAuth())
	{
		// adminApi.GET("/xxx", admin.Xxx)
		_ = admin.Placeholder
	}
}
```

---

## util/app/response/response.go（统一响应）

```go
package response

import (
	"<module>/app/enums/result"
	"<module>/util/i18nutil"
	"net/http"

	"github.com/gin-gonic/gin"
	i18n "github.com/suisrc/gin-i18n"
)

type Response struct {
	Status    int         `json:"status"`
	Message   string      `json:"message"`
	Data      interface{} `json:"data"`
	ErrorCode int         `json:"error_code"`
}

func returnResult(c *gin.Context, status int, msg string, data interface{}) {
	c.JSON(http.StatusOK, Response{Status: status, Message: msg, Data: data})
}

func Success(c *gin.Context, data interface{}) {
	returnResult(c, 0, i18nutil.SafeFormatText(c, &i18n.Message{ID: "Success"}), data)
}
func Fail(c *gin.Context, data interface{}) {
	returnResult(c, -1, i18nutil.SafeFormatText(c, &i18n.Message{ID: "Failed"}), data)
}
func FailWithMsg(c *gin.Context, msgID string, data interface{}) {
	returnResult(c, -1, i18nutil.SafeFormatText(c, &i18n.Message{ID: msgID}), data)
}
func TokenFail(c *gin.Context, data interface{}) {
	returnResult(c, 999, i18nutil.SafeFormatText(c, &i18n.Message{ID: "TokenError"}), data)
}
func PermissionDenied(c *gin.Context, data interface{}) {
	returnResult(c, 998, i18nutil.SafeFormatText(c, &i18n.Message{ID: "PermissionDenied"}), data)
}
func FailWithErrorCode(c *gin.Context, code result.ErrorCode) {
	c.JSON(http.StatusOK, Response{Status: -1, ErrorCode: code.Code, Message: code.Message})
}
```

## app/enums/result/error.go

```go
package result

type ErrorCode struct {
	Code    int
	Message string
}

// 在子包里集中声明业务错误，例如：
// var UserNotFound = ErrorCode{Code: 10001, Message: "user not found"}
```

## i18n 文件 lang/zh.toml

```toml
[Success]
other = "成功"

[Failed]
other = "失败"

[TokenError]
other = "登录已失效，请重新登录"

[PermissionDenied]
other = "无权访问"
```

`lang/en.toml` 提供英文版本。`util/i18nutil/SafeFormatText` 在缺翻译时回退默认文案。

## 不启用 i18n 时的简化方案

若用户不要 i18n：
- 删除 `r.Use(i18n.Serve(bundle))` 与 bundle 创建。
- `response.go` 把 `i18nutil.SafeFormatText(c, &i18n.Message{ID: "Success"})` 直接换成字符串字面量 `"success"` 等。
- `util/i18nutil` 整个目录可不建。
