---
title: 《索尼克大冒险 2》PC 版 Mod 制作教程：各文件/目录的大致作用
description: 了解 SA2 内部结构，解包相关软件与可选工具，详细介绍游戏内各种文件/目录功能
published: 2022-02-08T01:26:00.000Z
draft: true
category: Dev
tags: []
language: zh
---

## 注意事项

- 做 SA2 的 Mod 工作量巨大，请确保自己有足够的耐心再准备开始做这个项目
- 本文只阐述我对 SA2 Mod 制作过程的理解，可能存在不少错误，欢迎指出
- 本文不适用于任何人，请使用目录跳转功能

## 所需软件

### 必需

- Steam 上的[**《索尼克大冒险 2》**](https://store.steampowered.com/app/213610/Sonic_Adventure_2/)，如果可以的话强烈建议购买[**《Battle DLC》**](https://store.steampowered.com/app/217900/SONIC_ADVENTURE_2_BATTLE/)
- **SA2 Mod Loader**
- **SA Tools**

### 可选

- **NVIDIA Texture Tools Exporter**: 一个 PhotoShop 插件，用于修改 /resources/gd_PC 目录下 SOC 与 SOC 文件夹内经过 pak 压缩的 dds 文件 (图片/材质文件)
- ~~**SEGA Dreamcast Movie Creator**: 世嘉官方编写的 .avi 视频文件转换 .sfd 的工具，于 1999 年停止维护，需要 Windows 98/ME 系统~~
- [**SofdecVideoTools**](https://gamebanana.com/tools/13177): SEGA Dreamcast Movie Creator 替代品

## 游戏内各种文件/目录的作用

### Launcher.exe

游戏启动器, 此外还会生成游戏配置文件 Config/UserConfig.cfg 与键位配置文件 Config/Keyboard.cfg，做 Mod 一般用不着这个程序

### sonic2app.exe: 游戏主程序

以下文件(夹)需通过 SA Tools 解包获得

- advertise 游戏主要文件，包括菜单、GUI、屏幕、各个关卡对应的人物、String 字符串等
- Boss\* 各个 Boss 的材质信息与模型
- CartCommon 赛车关的材质信息与模型
- Chao 查欧相关
- Enemy 敌人相关
- Event 事件列表
- figure 各个关卡的评价分数、人物相关 (速度、攻击、配音等)
- Misc 音乐列表
- OBJECT 指定各个关卡所含的物品...?
- objects 通用物品...?
- Sounds 音频...
- stg\*\_\_\_ 各个关卡的材质信息与模型

### gd_PC

- event 事件文件，包括实时渲染的过场动画、CG (.sfd)、过场动画字幕等
- Font 不知道是哪里的字体...
- Message, MessageK 消息文件
- PRS 图片/材质文件
- SOC 图片/材质文件
- .prs 一种压缩格式
- .m1v 预先渲染的视频特效
- .sfd CG

### SAVEDATA 存档文件

- SONIC2B\_\_S01 游戏进度存档
- SONIC2B\_\_S02 查欧花园存档
