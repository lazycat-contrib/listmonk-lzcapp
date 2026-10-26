# listmonk-lzcapp

[listmonk](https://github.com/knadh/listmonk) 的 LazyCat LPK 打包 —— 高性能自托管邮件订阅与邮件列表管理系统。

## 形态

- 双容器：**listmonk-app**（`listmonk/listmonk`，端口 9000）+ **db**（`postgres:17-alpine`，内部服务）
- 镜像交付：mirror 模式（`docker.1ms.run/listmonk/listmonk:{tag}`），digest 校验开启
- 版本发现：Docker Hub 稳定三段式 tag（`^v[0-9]+[.][0-9]+[.][0-9]+$`），排除 v7.0.0-beta.x、nightly、beta 与各架构变体
- 仅喵喵商店；仅 PC 平台

## 首启安装

listmonk 的 Dockerfile 默认 CMD 只启动程序，不建表。manifest 里显式覆盖 command：

```
sh -c "./listmonk --install --idempotent --yes --config && ./listmonk --upgrade --yes --config && ./listmonk --config"
```

`--install --idempotent` 保证空库时只建一次表，`--upgrade` 在拉新镜像后自动跑数据库迁移。

## 部署参数

| 项 | 说明 |
|------|------|
| `admin_user` / `admin_password` | 首次启动自动创建的超管账号，登录页自动填充 |

数据库密码用 `stable_secret` 生成，两处引用同一密钥保持一致。

## 持久化

- `/lzcapp/var/uploads` → `/listmonk/uploads`（媒体库，在 Admin → Settings → Media 里指到这个路径）
- `/lzcapp/var/postgres` → `/var/lib/postgresql/data`

## 结构

- `package.yml` — LPK v2 包元数据 + 平台限制
- `lzc-manifest.yml` — 双服务、install command、免密登录注入
- `lzc-deploy-params.yml` — 超管账号部署参数
- `.github/lazycat-action.yml` — 镜像版本源，mirror 交付，仅私有商店
- `.github/workflows/lazycat.yml` — 每日自动跟版 + 发布喵喵商店
