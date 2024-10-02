---
title: 使用 Rclone 实现定期将 Mastodon 实例 PostgresSQL 数据库备份至 OneDrive
description:为避免意外情况对 Mastodon 实例数据造成的影响，最好应当对其进行定期备份...
published: 2024-10-2
category: Dev
tags: [Mastodon, Linux]
language: zh
---

根据 [Mastodon 说明文档](https://docs.joinmastodon.org/admin/backups)，为避免意外情况对 Mastodon 实例数据造成的影响，最好应当对其进行定期备份。

本文将介绍如何借助 Rclone 工具实现将 Mastodon 实例的 PostgresSQL 数据库备份至 OneDrive。

## 配置环境

1. 安装 Rclone
```bash
sudo -v ; curl https://rclone.org/install.sh | sudo bash
```

2. 使用命令 `rclone config` 进行配置，更多说明请见 [Rclone 文档](https://rclone.org/docs/)

## 自动化

以下为本人编写的 Bash 脚本，请根据实际情况修改变量 (PostgresSQL 数据库等信息可在 Mastodon 根目录 `.env.production` 文件中查看)

```bash
#!/bin/bash

# Setting Variables
LOCAL_BACKUP_DIR=""  # e.g. /var/backup/mastodon_db
DB_NAME="" # e.g. mastodon_production
REMOTE_BACKUP_PATH="" # e.g. Backup/mastodon_db
FILE_PREFIX="db_backup.${DB_NAME}"
DATE=$(date +\%Y-\%m-\%d)
BACKUP_FILE="${LOCAL_BACKUP_DIR}/${FILE_PREFIX}.${DATE}.gz"

# Dump database and upload to remote
sudo -u postgres pg_dump ${DB_NAME} | gzip > ${BACKUP_FILE}
rclone copy ${BACKUP_FILE} remote:${REMOTE_BACKUP_PATH}

# Remove local backup file
rm -f ${BACKUP_FILE}

# Remove backups older than 7 days
rclone delete remote:${REMOTE_BACKUP_PATH} --min-age 7d

# Backup log
echo "Backup created: ${BACKUP_FILE}" >> /opt/backup/db_backup.log
```

## 设置 crontab

```bash
crontab -e
```

添加以下内容，即可实现每天凌晨 2 点执行备份任务

```bash
0 2 * * * /bin/bash /path/to/backup_script.sh
```
