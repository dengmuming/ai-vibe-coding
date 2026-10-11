# 组件：xcli 命令行 + cron 定时任务 + main.go 入口

## main.go（入口）

```go
package main

import (
	"<module>/app/connection/mysql"
	"<module>/app/connection/redis"
	"<module>/app/console"
	"<module>/config"
	"<module>/global"
	"<module>/util/logger"

	"github.com/go-resty/resty/v2"
	"github.com/mix-go/xcli"
	"github.com/mix-go/xcli/flag"
)

func init() {
	initConfig()
}

func main() {
	// 命令行覆盖 host
	if h := flag.Match("host").String(); h != "" {
		config.GlobalConfig.System.Host = h
	}

	// --schedule=true 进入定时任务模式（独立进程）
	if flag.Match("schedule").Bool() {
		console.Schedule()
		return
	}

	// 否则交给 xcli
	commands := console.Commands
	for _, cmd := range commands {
		cmd.Options = append(cmd.Options, &xcli.Option{
			Names: []string{"init"},
			Usage: "需要加载的服务",
		})
	}
	xcli.SetName("app").
		SetVersion("1.0.0-alpha").
		SetDebug(config.GlobalConfig.System.Debug)
	xcli.AddCommand(commands...).Run()
}

func initConfig() {
	config.InitConfig()
	global.Logger = logger.Init("./log/log.log", "info")
	global.Db = mysql.GetDbClient()
	global.Redis = redis.GetRedisClient()
	global.RestyClient = resty.New()
}
```

## global/global.go

```go
package global

import (
	"<module>/app/connection/redis"

	"github.com/go-resty/resty/v2"
	"go.uber.org/zap"
	"gorm.io/gorm"
)

var (
	Logger      *zap.Logger
	Db          *gorm.DB
	Redis       *redis.MindCloudRedis
	RestyClient *resty.Client
)
```

## app/console/kernel.go

```go
package console

import (
	"<module>/app/console/commands"
	"time"

	"github.com/mix-go/xcli"
	"github.com/robfig/cron"
)

var Commands = []*xcli.Command{
	{
		Name:    "run",
		Short:   "运行 HTTP 服务",
		RunI:    &commands.ServerLoad{},
		Default: true,
		Options: []*xcli.Option{
			{Names: []string{"host"}, Usage: "服务监听地址，例：0.0.0.0:7781"},
		},
	},
	// 其它一次性命令注册示例：
	// { Name: "refreshTaskCount", Short: "刷新任务数", RunI: &commands.RefreshTaskCountCommand{} },
}

func Schedule() {
	loc, _ := time.LoadLocation("Asia/Shanghai")
	c := cron.NewWithLocation(loc)

	// 示例：每分钟执行一次
	// _ = c.AddFunc("0 */1 * * * *", func() {
	// 	commands.DelayedQueueCommand{}.Main()
	// })

	c.Start()
	for {
		time.Sleep(time.Second)
	}
}
```

## app/console/commands/serverLoad.go（HTTP 服务启动器）

```go
package commands

import (
	"<module>/config"
	"<module>/router"
)

type ServerLoad struct{}

func (t *ServerLoad) Main() {
	r := router.InitRouter()
	_ = r.Run(config.GlobalConfig.System.Host)
}
```

## 一次性命令模板（如 RefreshTaskCountCommand）

```go
package commands

import (
	"fmt"
	// "<module>/app/service/xxxService"
)

type RefreshTaskCountCommand struct{}

// Main
//
//	@Description: 一次性刷新任务数
func (RefreshTaskCountCommand) Main() {
	fmt.Println("[RefreshTaskCount] start")
	// 调用 service 层执行业务
	fmt.Println("[RefreshTaskCount] done")
}
```

注册到 `kernel.go` 的 `Commands` 列表后即可：

```bash
go run main.go refreshTaskCount
./<binary> refreshTaskCount
```

## 运行模式总结

| 命令 | 模式 |
|---|---|
| `go run main.go` 或 `./<binary>` | 默认 `run` 命令，启动 HTTP |
| `go run main.go --host=0.0.0.0:8080` | 覆盖端口 |
| `go run main.go --schedule=true` | 定时任务（cron），独立进程 |
| `go run main.go <commandName>` | 一次性命令 |

## cron 注意事项

- 时区固定 `Asia/Shanghai`（按业务需要调整）。
- **多实例部署时，定时任务必须只在一台跑**，否则会出现重复触发（如队列回投会与下游 worker 抢同一份任务）。
- cron 表达式用 6 段（秒级），库版本是 `github.com/robfig/cron`（v1）。如升级到 `cron/v3` 是 5 段，注意区分。
