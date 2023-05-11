---
title: "Vaultwarden 备份"
date: 2023-05-11T16:09:32+08:00
description: ""
featuredImage: ""
categories:
- tutorial
tags:
- vaultwarden
- bitwarden
- docker
- shell
---

## 前言

[三年前开始](../bitwarden-install/) 使用 [Vaultwarden](https://github.com/dani-garcia/vaultwarden)(非官方的 [Bitwarden](https://bitwarden.com/))，但是始终没有对它进行完整的备份操作，偶尔记得就到处备份一下，心里总感觉有点虚，一方面容易泄漏数据，另一方面害怕那天不然硬件暴毙东西就全没了。今天闲下来就对 Vaultwarden 进行一下完整的备份。

## 思路

测试了一下将整个目录导出后，邮箱信息、2FA 等都完整保留，就没有像大多数人一样只备份 `数据库` 和 `attachments` 目录，就直接压缩打包整个目录到指定目录下即可

> 由于群晖的 `CloudSync` 套件支持加密备份到各类云或 Webdav，备份到指定目录下也可以实现自动加密备份到云，实现数据脱离硬件备份，防止硬件暴毙丢失关键数据

## docker compose

由于一开始使用群晖 docker 套件启动的容器，为了后续方便的进行恢复操作，根据 [官方 WIKI](https://github.com/dani-garcia/vaultwarden/wiki) 写了下 `docker-compose.yml`

```yml
version: '3'

services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: always
    environment:
      WEBSOCKET_ENABLED: "true"  # Enable WebSocket notifications.
      ROCKET_PROFILE: release
      ROCKET_ ADDRESS: 0.0.0.0
      ROCKET_PORT: 80
      SMTP_HOST:
      SMTP_FROM:
      SMTP_PORT:
      SMTP_USERNAME:
      SMTP_PASSWORD:
      SMTP_SECURITY:
      SIGNUPS_ALLOWED: "false"
      INVITATIONS_ALLOWED: "false"
    volumes:
      - ./data:/data
    ports:
      - xxxx:3012
      - xxxx:80
```

## 备份脚本

现在写 Shell 就不需要一点一点搜索测试了，直接 chatgpt，说明需求后直接给出脚本代码，雀食爽

```shell
#!/bin/bash

# 设置全局变量
SRC_DIR=/path/to/source/dir
DST_DIR=/path/to/destination/dir
MAX_NUM=30
LOG_FILE=${DST_DIR}/Error_Log_$(date +%Y%m%d_%H%M%S).log
ARCHIVE_FILE=${DST_DIR}/$(basename ${SRC_DIR})_$(date +%Y%m%d_%H%M%S).tar.gz
BARK_URL=https://api.day.app/{yourkey}/Bitwarden备份出错/

# 如果目标目录不存在则创建目标目录
if [ ! -d "${DST_DIR}" ]; then
    mkdir -p ${DST_DIR}
fi

# 打log函数
function log {
    echo "$(date +'%Y-%m-%d %H:%M:%S') $1" >> ${LOG_FILE}
    if [[ $1 == *"错误"* || $1 == *"出现"* ]]; then
        echo "$(date +'%Y-%m-%d %H:%M:%S') $1" >> ${DST_DIR}/ErrorLog_$(date +%Y%m%d_%H%M%S).log
        # bark发送通知
        curl "${BARK_URL}/${1}_$(date +%Y%m%d_%H%M%S)"
    fi
}

# 压缩函数
function compress {
    echo "开始压缩 ${SRC_DIR}"
    tar -czvf ${ARCHIVE_FILE} ${SRC_DIR} > /dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo "压缩完成，文件存储在 ${ARCHIVE_FILE}"
    else
        log "压缩出现错误，请检查！"
    fi
}

# 删除旧压缩包函数
function delete_old_archives {
    num=$(ls -l ${DST_DIR} | grep "^-" | wc -l)
    while [ ${num} -gt ${MAX_NUM} ]
    do
        file=$(ls -rt ${DST_DIR}/*_*.tar.gz | head -n 1)
        if [ -n "${file}" ]; then
            rm -f "${file}"
            echo "删除旧文件 ${file}"
        else
            echo "没有找到旧文件"
            break
        fi
        let num--
    done
}

# 主函数
function main {
    compress
    delete_old_archives
}

# 执行主函数
main

```

## 执行

由于我是使用群晖，就直接使用计划任务，定时执行脚本即可，如果是直接在云服务器上运行的话，也可以使用 `crontab` 命令来执行

```shell
# 每天凌晨1点执行脚本
(crontab -l ; echo "0 1 * * * /bin/bash /path/to/your/script.sh >/dev/null 2>&1") | crontab -
```

