# LovelyCarve Linux 取证分析插件（`org.lovelycarve.linuxinfo` v3.0.0）

插件 ID：`org.lovelycarve.linuxinfo`（LPP v2，`v3.0.0`）

> 本仓库仅提供面向 **LovelyCarve** 的 Release `.lcpkg` 二进制插件包发布与使用说明，不包含源码。

## 下载与安装

请前往本仓库 **[Releases](../../releases)** 页面下载最新版本的 `org.lovelycarve.linuxinfo-3.0.0-windows-x64.lcpkg`（[`v3.0.0`](../../releases/tag/v3.0.0)）。

> - **官方与二开通用**：本插件基于标准 LPP v2 协议（无魔改专属扩展，`schema_hash: fnv1a64:44dd26f6c7d59917`），**官方主项目 [LovelyTeams/LovelyCarve](https://github.com/LovelyTeams/LovelyCarve) 与二开版 [JerryXie599/LovelyCarve-Mod](https://github.com/JerryXie599/LovelyCarve-Mod) 通用**。
> - **自动替代内置插件、无重复结果**：本插件使用标准 ID `org.lovelycarve.linuxinfo`（`v3.0.0`），安装后直接覆盖升级自带基础 Linux 插件，避免同时跑出两份重复结果。
> - **旧版迁移提示**：如你此前安装过旧 ID `org.lovelycarve.linuxforensics`（`v1.0.0` / `v1.1.0`），请先卸载它（`lovelycarve-cli plugin rm org.lovelycarve.linuxforensics` 或在插件页卸载），仅保留 `org.lovelycarve.linuxinfo` `v3.0.0` 即可。

### 安装方式
- **图形界面**：打开 LovelyCarve → **「设置 → 插件 → 安装 .lcpkg」**，选择下载的 `.lcpkg` 文件。
- **命令行**：
  ```bash
  lovelycarve-cli plugin install org.lovelycarve.linuxinfo-3.0.0-windows-x64.lcpkg
  ```

---

LovelyCarve LPP v2 综合 Linux 深度取证插件，面向桌面系统、嵌入式设备（如树莓派）、企业 Web/容器/数据库服务器及 NAS 存储检材提供一站式深度解析与时间线重建。

## 支持的分析作用域

- **`linuxbaseinfo` (`system` 视图)**：针对已挂载识别的 Linux 系统自动执行 15 大取证维度的深度分析，并提供 `timezone`、`hostname`、`users` 画像事实。
- **`linux_evidence` (`evidence` 视图)**：全盘扫描所有卷中的 Linux 根目录、WSL2 子系统、NAS 数据卷、运维面板、数据库集群及抓包归档。
- **`linux_files` (`files` 视图)**：支持右键单文件/多文件直接解析（`wtmp`/`btmp`/`lastlog`/`wtmp.db`、`.journal`、`.gz` 轮转日志、`nginx.conf`/`access.log`、`default.db`/`1Panel.db`、`dump.rdb`、`openclaw.json`、`.pem`/`.crt` 证书及 `.pcap`/`.pcapng`）。

## 核心取证覆盖矩阵

1. **系统身份与硬件指纹**：`/etc/os-release`（自动回退 `/usr/lib/os-release`）、`/boot/firmware/issue.txt`、`/etc/machine-id`、`/etc/timezone`/`/etc/localtime` UTC 偏移解析、`journald.conf`、`lib/modules` 内核与硬件 `Machine model` 提取。
2. **账户、Shadow 盐值与安全策略**：`/etc/passwd` + `/etc/shadow` 关联解析（精确提取 `$1$`/`$5$`/`$6$`/`$y$`/`$2b$` 算法、**Salt 盐值**与改密日期）、`/etc/group` 成员统计、`sshd_config`、`sudoers`、`pam_exec`、`audit.rules` 审计规则。
3. **网络、Wi-Fi 明文密码与租赁**：Netplan YAML、NetworkManager `.nmconnection`（提取 Wi-Fi SSID 与明文 PSK）、`wpa_supplicant.conf`、`ifcfg-*`、`resolv.conf`、`hosts`、DHCP 租约与 Avahi IPv6 记录。
4. **存储挂载、RAID、LVM 与 NAS**：`/etc/fstab`、`/etc/mdadm/mdadm.conf`、LVM PV/VG/LV 元数据、OpenMediaVault `/etc/openmediavault/config.xml`、Samba `smb.conf`、NFS `/etc/exports` 与群晖配置。
5. **登录与系统/安全/Journal 日志**：二进制 `wtmp`/`btmp`/`lastlog`、Debian 13 SQLite `wtmp.db`、`.gz` 轮转 `auth.log`/`secure`/`syslog`/`kern.log`/`audit.log`（含 Hex 命令解码与 RAID/EXT4/USB/Wi-Fi SAE 事件），以及完整支持 **Classic / Compact / LZ4 / ZSTD** 的二进制 `systemd-journal` 解析器。
6. **Shell、终端录屏与现场留存**：`.bash_history`/`.zsh_history`/数据库历史、`.bashrc` 历史策略配置、`script` 终端会话日志、无线监控扫描结果及现场留存文本。
7. **软件包时间线与自启动**：`dpkg/status`、`dpkg.log*`/`apt/history.log*`/`yum.log*`/`dnf.log*`/`rpmdb.sqlite` 安装升级时间线、Systemd Service/Timer/Wants、`init.d`、`rc.local` 与 Crontab。
8. **Web 服务与日志审计**：Nginx/OpenResty/Apache 虚拟主机配置（含 `sites-enabled` 启用状态、TLS 协议与反代目标）及 Combined / JSON（含设备与应用字段）/ Error 访问与攻击特征日志解析。
9. **运维面板**：宝塔面板（`default.db`/`port.pl`/`admin_path.pl`/`userInfo.json`）、1Panel（`1Panel.db`）、小皮面板与 LAMPP Marker。
10. **容器与编排**：Docker `config.v2.json`、`hostconfig.json`、`daemon.json`、`*-json.log`、`repositories.json`、`docker-compose.yml`、LXC 与 Kubernetes 配置。
11. **数据库与环境密钥**：PostgreSQL `PGDATA` 纯 Rust 页元组读取（`pg_database`、`pg_class` 行估计与 Fernet `gAAAAA` 时间戳提取）与 `pg_hba.conf`、`my.cnf` 与表文件、`redis.conf` & `dump.rdb`、`.env*`/OAuth `*token*.json`/`package.json`/源码数据库连接与路由。
12. **AI Agent 栈**：OpenClaw `openclaw.json`（模型、Provider BaseURL/API Key、MCP Servers、渠道白名单）、`cron/jobs.json` 报错与调度状态、`openclaw-agent.sqlite`、`memory/main.sqlite`、会话 `.jsonl` 与工作区文档，以及 Claude/Codex/Ollama。
13. **浏览器与桌面/远程痕迹**：Firefox、Chrome/Chromium/Edge/Brave/QAX/360/UOS 浏览器、GTK `recently-used.xbel`、`.desktop`、`Trash/*.trashinfo`、Kylin/Deepin 便签/语音、GNOME Keyring、`~/.ssh/*` 密钥指纹、Remmina、FileZilla、ToDesk。
14. **X.509 证书与 PCAP/PCAPNG**：纯 Rust ASN.1 DER/PEM 证书（Subject/Issuer/Validity/SAN DNS & IP）与 PCAP/PCAPNG（统计摘要、TCP 分段重组明文 HTTP 请求与载荷预览、DHCP/mDNS 报文）。
