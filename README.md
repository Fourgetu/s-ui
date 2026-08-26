# S-UI（改版）

**基于 [SagerNet/sing-box](https://github.com/SagerNet/sing-box) 的高级 Web 面板**

> 本仓库是 [alireza0/s-ui](https://github.com/alireza0/s-ui) 的二次开发分支。当前已同步官方 `v1.5.5`，并保留全自动安装、协议模板、行内二维码、批量删除、中转和多节点管理等魔改功能。
>
> 仅供个人学习与交流，请勿用于非法用途，请勿用于生产环境。上游版权归原作者所有。

[![License](https://img.shields.io/badge/license-GPL%20V3-blue.svg?longCache=true)](https://www.gnu.org/licenses/gpl-3.0.en.html)

---

## 快速开始

### 全自动安装（推荐）

全程无需交互。全新安装会自动生成随机管理员账号、密码和面板路径，并在完成后打印访问信息；升级时保留已有设置。

```sh
bash <(curl -Ls https://raw.githubusercontent.com/Fourgetu/s-ui/main/install.sh)
```

### 交互式安装

需要手动设置端口、路径、账号和密码时，关闭默认的全自动模式：

```sh
SUI_AUTO=0 bash <(curl -Ls https://raw.githubusercontent.com/Fourgetu/s-ui/main/install.sh)
```

安装完成后，在服务器上输入 `s-ui` 可打开管理菜单。

### 安装器语言

安装器支持 `en`、`fa`、`ru`、`vi`、`zhcn`、`zhtw`。未指定时会根据系统 `$LANG` 自动选择：

```sh
SUI_LANG=zhcn bash <(curl -Ls https://raw.githubusercontent.com/Fourgetu/s-ui/main/install.sh)
```

### Alpine Linux

Alpine 没有预装 Bash，需先安装；脚本会自动配置 OpenRC 服务：

```sh
apk add bash
bash <(curl -Ls https://raw.githubusercontent.com/Fourgetu/s-ui/main/install.sh)
```

服务管理命令：`rc-service s-ui start|stop|restart`。

### 安装指定版本

```sh
VERSION=v1.5.5-custom.1 && bash <(curl -Ls https://raw.githubusercontent.com/Fourgetu/s-ui/$VERSION/install.sh) $VERSION
```

> 指定版本安装要求 [Fourgetu/s-ui Releases](https://github.com/Fourgetu/s-ui/releases) 中存在相同标签和对应架构的安装包。

### Windows

1. 从 [Fourgetu/s-ui Releases](https://github.com/Fourgetu/s-ui/releases/latest) 下载最新 Windows 包并解压。
2. 以管理员身份运行 `install-windows.bat`，按向导完成。

---

## 默认安装信息

| 项目 | 默认值 |
| --- | --- |
| 面板端口 | `2095` |
| 面板路径 | `/app/` |
| 订阅端口 | `2096` |
| 订阅路径 | `/sub/` |
| 账号 / 密码 | `admin` |

> 全自动安装会使用随机账号、密码和面板路径，请保存安装结束时打印的信息。升级已有数据库时不会修改账号和路径。

---

## 与上游的差异

- **Fork 基建**：安装、更新脚本和 CI Release 指向 `Fourgetu/s-ui`；默认全自动安装。
- **协议与管理体验**：常用协议一键模板、二维码、批量删除、导出中心和多套面板皮肤。
- **中转**：从分享链接生成 outbound，并自动配置路由。
- **多节点管理**：中央面板通过 APIv2 令牌管理远程 S-UI 节点。
- **安装增强**：自动选择空闲端口、开放防火墙、下载重试与镜像回退。

后续更新采用 `upstream/alireza0/s-ui` 定期合并，保留本仓库的独立魔改提交。

## 支持的平台

| 平台 | 架构 | 状态 |
| --- | --- | --- |
| Linux | amd64 / arm64 / armv7 / armv6 / armv5 / 386 / s390x | ✅ |
| Windows | amd64 / 386 / arm64 | ✅ |
| macOS | amd64 / arm64 | 🚧 实验性 |

## 功能概览

- 多协议：Mixed、SOCKS、HTTP、Direct、Redirect、TProxy、VLESS、VMess、Trojan、Shadowsocks、ShadowTLS、Hysteria、Hysteria2、Naive、TUIC、AnyTLS。
- 入站、出站和流量路由高级配置。
- 客户端流量上限、到期时间、在线状态与系统监控。
- Link、JSON、Clash 订阅及外部订阅。
- 多语言、明暗主题、API 接口和 HTTPS。

---

## Docker 安装

<details>
<summary>展开</summary>

```shell
# 安装 Docker
curl -fsSL https://get.docker.com | sh

# 运行 S-UI（GHCR 镜像）
mkdir s-ui && cd s-ui
docker run -itd \
    -p 2095:2095 -p 2096:2096 -p 443:443 -p 80:80 \
    -v $PWD/db/:/app/db/ \
    -v $PWD/cert/:/root/cert/ \
    --name s-ui --restart=unless-stopped \
    ghcr.io/fourgetu/s-ui:latest
```

自行构建镜像：

```shell
git clone --recurse-submodules https://github.com/Fourgetu/s-ui
cd s-ui
docker build -t s-ui .
```

</details>

## 卸载

直接运行 `s-ui`，选择卸载；也可以按系统手动清理。

### systemd

```sh
systemctl disable s-ui --now
rm -f /etc/systemd/system/s-ui.service
rm -fr /usr/local/s-ui /etc/s-ui
rm -f /usr/bin/s-ui
systemctl daemon-reload
```

### Alpine（OpenRC）

```sh
rc-service s-ui stop
rc-update del s-ui default
rm -f /etc/init.d/s-ui
rm -fr /usr/local/s-ui /etc/s-ui
rm -f /usr/bin/s-ui
```

---

## 本地开发

<details>
<summary>展开</summary>

```shell
git clone --recurse-submodules https://github.com/Fourgetu/s-ui
cd s-ui
./runSUI.sh
```

分步构建：

```shell
cd frontend && npm install && npm run build && cd ..
rm -fr web/html/* && cp -R frontend/dist/* web/html/
go build -o sui main.go
./sui
```

魔改前端位于独立子模块：[Fourgetu/s-ui-frontend](https://github.com/Fourgetu/s-ui-frontend)。

</details>

## 环境变量

| 变量 | 取值 | 默认值 |
| --- | --- | --- |
| `SUI_LOG_LEVEL` | `debug` / `info` / `warn` / `error` | `info` |
| `SUI_DEBUG` | `true` / `false` | `false` |
| `SUI_BIN_FOLDER` | 字符串 | `bin` |
| `SUI_DB_FOLDER` | 字符串 | `db` |
| `SINGBOX_API` | 字符串 | - |

---

## 致谢

- 上游项目：[alireza0/s-ui](https://github.com/alireza0/s-ui)、[alireza0/s-ui-frontend](https://github.com/alireza0/s-ui-frontend)
- 魔改前端：[Fourgetu/s-ui-frontend](https://github.com/Fourgetu/s-ui-frontend)
- 内核：[SagerNet/sing-box](https://github.com/SagerNet/sing-box)
- [API 文档（上游 Wiki）](https://github.com/alireza0/s-ui/wiki/API-Documentation)
