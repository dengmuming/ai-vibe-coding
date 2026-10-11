# 组件：配置（yaml）与日志（zap）

## config 包

`config/config.go`：YAML 解析，全局单例 `GlobalConfig`。

```go
package config

import (
	"io/ioutil"

	"gopkg.in/yaml.v2"
)

type System struct {
	Host      string `yaml:"host"`
	Debug     bool   `yaml:"debug"`
	LimitSize int64  `yaml:"limit_size"`
}

type Mysql struct {
	User     string `yaml:"user"`
	Host     string `yaml:"host"`
	Password string `yaml:"password"`
	Port     string `yaml:"port"`
	Name     string `yaml:"name"`
	Charset  string `yaml:"charset"`
}

type Redis struct {
	Host     string `yaml:"host"`
	Password string `yaml:"password"`
	Port     string `yaml:"port"`
}

// 按需追加 Cos / AliCloud / Api / ... 子结构

type Configure struct {
	System *System `yaml:"system"`
	Mysql  *Mysql  `yaml:"mysql"`
	Redis  *Redis  `yaml:"redis"`
}

var GlobalConfig *Configure

func InitConfig() {
	file, err := ioutil.ReadFile("config.yaml")
	if err != nil {
		return
	}
	_ = yaml.Unmarshal(file, &GlobalConfig)
}
```

### 配套 `example.config.yaml`

```yaml
system:
  debug: true
  host: "0.0.0.0:7781"
  limit_size: 3072
mysql:
  user: "root"
  password: "123456"
  host: "127.0.0.1"
  port: "3306"
  name: "your_db"
  charset: "utf8"
redis:
  host: "127.0.0.1"
  password: ""
  port: "6379"
```

### 约束

- 字段必须有 `yaml` tag，命名 snake_case。
- 子结构用指针，避免空段被零值覆盖。
- 新增配置项的顺序：**struct 字段 → example.config.yaml → 业务代码**。

---

## logger 包

`util/logger/logger.go`：zap + lumberjack 滚动写入。

```go
package logger

import (
	"os"

	"github.com/natefinch/lumberjack"
	"go.uber.org/zap"
	"go.uber.org/zap/zapcore"
)

var levelMap = map[string]zapcore.Level{
	"debug": zap.DebugLevel,
	"info":  zap.InfoLevel,
	"warn":  zap.WarnLevel,
	"error": zap.ErrorLevel,
	"fatal": zap.FatalLevel,
}

func Init(dir string, level string) *zap.Logger {
	return newLogger(dir, levelMap[level], false)
}

func newLogger(filePath string, level zapcore.Level, compress bool) *zap.Logger {
	core := newCore(filePath, level, compress)
	return zap.New(core, zap.AddCaller(), zap.Development())
}

func newCore(filePath string, level zapcore.Level, compress bool) zapcore.Core {
	hook := lumberjack.Logger{
		Filename:   filePath,
		Compress:   compress,
		MaxSize:    1,  // MB
		MaxBackups: 30,
	}
	atomicLevel := zap.NewAtomicLevel()
	atomicLevel.SetLevel(level)
	encoderConfig := zapcore.EncoderConfig{
		TimeKey:        "time",
		LevelKey:       "level",
		NameKey:        "logger",
		MessageKey:     "msg",
		CallerKey:      "caller",
		StacktraceKey:  "stacktrace",
		LineEnding:     zapcore.DefaultLineEnding,
		EncodeLevel:    zapcore.LowercaseLevelEncoder,
		EncodeTime:     zapcore.TimeEncoderOfLayout("2006-01-02 15:04:05.000"),
		EncodeDuration: zapcore.SecondsDurationEncoder,
		EncodeCaller:   zapcore.FullCallerEncoder,
		EncodeName:     zapcore.FullNameEncoder,
	}
	return zapcore.NewCore(
		zapcore.NewJSONEncoder(encoderConfig),
		zapcore.NewMultiWriteSyncer(zapcore.AddSync(os.Stdout), zapcore.AddSync(&hook)),
		atomicLevel,
	)
}
```

### 使用

```go
// main.go init()
global.Logger = logger.Init("./log/log.log", "info")

// 业务包
global.Logger.Info("user login", zap.Int("uid", 123))
global.Logger.Error("db error", zap.Error(err))
```

### 约束

- `./log/` 必须 `.gitignore`，运行时由 lumberjack 创建。
- 不得在业务包内 `zap.NewProduction()` 自建 logger。
