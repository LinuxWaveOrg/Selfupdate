# LinuxWaveOrg/Selfupdate

LinuxWave 的自更新数据。它只回答两个问题：

1. **现在该装哪个版本？** → `linuxwavedata/latest.toml`
2. **那个版本具体怎么装？** → `linuxwavedata/<版本>/update_commands.sh`

客户端只读这一个仓库，地址由环境变量决定：

| 环境变量 | 默认值 | 用途 |
| --- | --- | --- |
| `LINUXWAVE_SELFUPDATE_DOMAIN` | 本仓库的 raw 地址 | 版本指针与更新脚本 |

自带解释器不在这里，见 [`LinuxWaveOrg/configdata`](https://github.com/LinuxWaveOrg/configdata)
（`LINUXWAVE_PYTHON_DOMAIN`）。两者分开是因为体积与发布节奏不同：一个几十字节，
一个每架构几十 MB。

## 目录

```
linuxwavedata/
    latest.toml                这次要装 / 升到哪个版本（唯一入口）
    latest.toml.sig            对上面那份的 RSA/SHA-256 分离签名
    4.4.0/
        update_commands.sh     该版本「下载哪些文件、放到哪里」，随版本冻结
```

## 为什么分成两个文件

`latest.toml` 每次发版都变，`update_commands.sh` 按版本冻结。

混在一个文件里就做不到「装了旧版本的用户仍在运行，他们那份更新说明不能因为
发了新版就变样」。

## 签名

`latest.toml.sig` 由发布私钥签（RSA，`openssl dgst -sha256 -verify` 验），
验签用的公钥**内嵌在**客户端的 `lib/install.sh` / `lib/selfupdate.sh` 里，
不读安装树 —— 能换数据的人也能换放在它旁边的公钥。
