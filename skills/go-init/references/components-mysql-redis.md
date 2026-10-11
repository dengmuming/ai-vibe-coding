# 组件：MySQL（GORM） 与 Redis

## MySQL

`app/connection/mysql/mysql.go`：返回 `*gorm.DB`，由 `main.go` 装入 `global.Db`。

```go
package mysql

import (
	"<module>/config"

	"gorm.io/driver/mysql"
	"gorm.io/gorm"
	"gorm.io/gorm/schema"
)

func GetDbClient() *gorm.DB {
	c := config.GlobalConfig.Mysql
	dsn := c.User + ":" + c.Password + "@tcp(" + c.Host + ":" + c.Port + ")/" +
		c.Name + "?charset=" + c.Charset + "&parseTime=True&loc=Local"

	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
		NamingStrategy: schema.NamingStrategy{
			SingularTable: true, // 单数表名
		},
	})
	if err != nil {
		return nil
	}
	return db
}
```

### model 包写法

`app/model/userModel/user.go`（统一 CRUD 套路）：

```go
package userModel

import (
	"<module>/global"
	"time"
)

type User struct {
	ID        int    `json:"id"`
	Name      string `json:"name"`
	CreatedAt int64  `json:"created_at"`
	UpdatedAt int64  `json:"updated_at"`
}

func Add(data User) (User, error) {
	data.CreatedAt = time.Now().Unix()
	data.UpdatedAt = time.Now().Unix()
	err := global.Db.Model(&User{}).Create(&data).Error
	return data, err
}

func Get(where map[string]interface{}) (User, error) {
	var data User
	err := global.Db.Model(&User{}).Where(where).First(&data).Error
	return data, err
}

func Edit(where map[string]interface{}, data interface{}) error {
	return global.Db.Model(&User{}).Where(where).Updates(data).Error
}

func Delete(where map[string]interface{}) error {
	return global.Db.Model(&User{}).Where(where).Delete(&User{}).Error
}

func Count(where map[string]interface{}) int {
	var total int64
	global.Db.Model(&User{}).Where(where).Count(&total)
	return int(total)
}

func GetList(where string, start, limit int) ([]User, int) {
	var list []User
	var total int64
	global.Db.Model(&User{}).Where(where).Order("id desc").Offset(start).Limit(limit).Find(&list)
	global.Db.Model(&User{}).Where(where).Count(&total)
	return list, int(total)
}

func GetAllList(where string) []User {
	var list []User
	global.Db.Model(&User{}).Where(where).Order("id desc").Find(&list)
	return list
}
```

### 约束

- 表名单数（与 `SingularTable: true` 对齐）。
- 时间戳用 `int64` Unix 秒，由 model 在 `Add/Edit` 时填，不依赖 GORM 自动 hook。
- model 函数签名固定：`Add/Get/Edit/Delete/Count/GetList(where,start,limit)/GetAllList(where)`，便于跨域统一。

---

## Redis

`app/connection/redis/redis.go`：自封装的 `MindCloudRedis`，对外暴露常用方法，避免 controller/service 直接接触 `go-redis`。

```go
package redis

import (
	"context"
	"<module>/config"
	"time"

	"github.com/go-redis/redis/v8"
)

var ctx = context.Background()
var ExpirationTime = 10 * time.Hour

type MindCloudRedis struct {
	client *redis.Client
}

func GetRedisClient() *MindCloudRedis {
	c := config.GlobalConfig.Redis
	rdb := redis.NewClient(&redis.Options{
		Addr:     c.Host + ":" + c.Port,
		Password: c.Password,
		DB:       0,
	})
	return &MindCloudRedis{client: rdb}
}

// 直接拿原生 client（Sorted Set / Lua 等高阶用法）
func (c *MindCloudRedis) GetClient() *redis.Client    { return c.client }
func (c *MindCloudRedis) GetContext() context.Context { return ctx }

// 常用封装
func (c *MindCloudRedis) Get(key string) interface{} { /* ... */ return nil }
func (c *MindCloudRedis) Set(key string, v interface{}, exp time.Duration) error { /* ... */ return nil }
func (c *MindCloudRedis) Del(key string)                                          { c.client.Del(ctx, key) }
func (c *MindCloudRedis) LPush(key string, v interface{}) error                   { return c.client.LPush(ctx, key, v).Err() }
func (c *MindCloudRedis) RPop(key string) (string, error)                         { return c.client.RPop(ctx, key).Result() }
func (c *MindCloudRedis) Exists(key string) bool {
	val, err := c.client.Exists(ctx, key).Result()
	return err == nil && val == 1
}

// Remember：缓存击穿包装
func (c *MindCloudRedis) Remember(key string, exp time.Duration, cb func() interface{}) interface{} {
	if v := c.Get(key); v != nil {
		return v
	}
	v := cb()
	if v != nil {
		_ = c.Set(key, v, exp)
	}
	return v
}
```

### Redis Key 集中管理

`app/enums/redisKey.go`：

```go
package enums

const (
	RedisKeyUserToken     = "user:token:%d"   // 占位用 fmt.Sprintf 拼接
	RedisKeyTaskQueue     = "task:queue"
	RedisKeyTaskDelayed   = "task:queue:delayed"
)
```

### 约束

- 业务包**不得**直接 `import "github.com/go-redis/redis/v8"`，统一通过 `global.Redis.XXX()`。
- 需要原生 API（如 ZAdd/Lua）时，调 `global.Redis.GetClient()`，仍把 key 常量放 `enums/redisKey.go`。
- 字符串 key 严禁散落，必须落到 `redisKey.go`。
