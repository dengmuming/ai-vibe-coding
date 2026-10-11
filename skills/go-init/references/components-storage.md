# 组件（可选）：对象存储 OSS / COS / STS

仅在用户明确需要文件上传/下载/前端直传/STS 临时凭证时启用。

## 依赖

```
github.com/aliyun/aliyun-oss-go-sdk
github.com/aliyun/alibaba-cloud-sdk-go
github.com/tencentyun/cos-go-sdk-v5
github.com/tencentyun/qcloud-cos-sts-sdk
```

## 配置

在 `config/config.go` 增加：

```go
type Cos struct {
	SecretId  string `yaml:"secret_id"`
	SecretKey string `yaml:"secret_key"`
	Region    string `yaml:"region"`
	Bucket    string `yaml:"bucket"`
	AppId     string `yaml:"app_id"`
}

type AliCloud struct {
	Oss struct {
		Region             string `yaml:"region"`
		StsRoleArn         string `yaml:"sts_role_arn"`
		StsRoleArnOnlyRead string `yaml:"sts_role_arn_only_read"`
		Endpoint           string `yaml:"endpoint"`
		AccessKeyId        string `yaml:"access_key_id"`
		AccessKeySecret    string `yaml:"access_key_secret"`
		MCBucket           string `yaml:"mc_bucket"`
	} `yaml:"oss"`
}
```

`example.config.yaml`：

```yaml
cos:
  secret_id: "xxx"
  secret_key: "xxx"
  region: "ap-shenzhen"
  bucket: "your-bucket-1300000000"

ali_cloud:
  oss:
    region: "cn-shenzhen"
    sts_role_arn: "acs:ram::1000000000000000:role/uploadrole"
    endpoint: "http://oss-cn-shenzhen.aliyuncs.com"
    access_key_id: "xxx"
    access_key_secret: "xxx"
    mc_bucket: "your-bucket"
```

## 目录划分

```
util/
├── oss/        # 阿里云 OSS 工具：签名 URL、STS、上传下载
└── cos/        # 腾讯云 COS 工具：签名 URL、STS

app/service/
├── cosService/         # 业务封装：URL 续签、metadata 等
└── storage/            # 抽象存储接口（多云切换）
```

## 落地建议

1. `util/oss` `util/cos` 只放**与 SDK 直接交互**的薄封装：构造 client、生成签名 URL、申请 STS、上传/下载/copy。
2. `app/service/storage` 定义统一接口（如 `type Storage interface { Upload(...); SignedURL(...) }`），实现切换 OSS/COS。controller/service 调接口而非 SDK。
3. **STS 临时凭证必须缓存**：用 `global.Redis.Remember` 缓存到 key 过期前 5~10 分钟，避免每次请求都签发。
4. 上传/下载的 controller 走通用接口：`/api/v1/common/getOssTemporaryKey`、`/api/v1/common/getCosTemporaryKey`，由前端拿到临时 KS 直传。

## STS 缓存示例伪代码

```go
func GetOssStsCached() (StsCredential, error) {
	key := "oss:sts:write"
	v := global.Redis.Remember(key, 50*time.Minute, func() interface{} {
		cred, err := ossUtil.AssumeRole(/*...*/)
		if err != nil {
			return nil
		}
		return cred
	})
	if v == nil {
		return StsCredential{}, errors.New("sts unavailable")
	}
	return v.(StsCredential), nil
}
```

## 不启用时

整个 `util/oss` `util/cos` `app/service/cosService` `app/service/storage` 子树不创建，配置中也不引入对应 struct。
