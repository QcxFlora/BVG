> **重要声明：BiliFlora 面向中国大陆以外用户使用。如有中国大陆地区的相关使用需求，请使用哔哩哔哩官方提供的[云视听小电视](https://app.bilibili.com)。**
> 


<div align="center">



# BiliFlora



**Flutter UI · Rust Core · Android TV & Mobile**



一个面向 Android TV 与 Android 手机的 Bilibili 第三方客户端重构项目。



[![Android](https://img.shields.io/badge/Android-5.0%2B-3DDC84?logo=android&logoColor=white)](https://developer.android.com/) [![Flutter](https://img.shields.io/badge/Flutter-UI-02569B?logo=flutter&logoColor=white)](https://flutter.dev/) [![Rust](https://img.shields.io/badge/Rust-Core-000000?logo=rust&logoColor=white)](https://www.rust-lang.org/) [![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)



</div>



## 项目简介



BiliFlora 从 [Frost819/bv](https://github.com/Frost819/bv) Fork 而来，参考 [PiliPlus](https://github.com/bggRGjQaUbCoE/PiliPlus) 的 Flutter 客户端组织方式，对原有 Android TV 客户端进行分层重构。项目在保留 TV 遥控器焦点导航、视频播放和 Bilibili 内容浏览能力的基础上，建立便于测试、维护和跨设备扩展的 Flutter/Rust 架构。



Flutter 负责页面展示、焦点管理、手机点击与手势以及播放器交互；Rust 负责可测试的核心数据管线，包括 API 数据模型、弹幕与字幕解析、时间轴排序、规范化、屏蔽过滤、缓存和观看历史。



## 平台支持



| 平台 | 状态 | 交互方式 |

| --- | --- | --- |

| Android TV | 目标平台 | 遥控器方向键、Select、返回键与焦点导航 |

| Android 手机 | 目标平台 | 点击、滑动、播放器手势与系统返回 |

| Android 32 位 | 目标 ABI | `armeabi-v7a` |

| Android 64 位 | 目标 ABI | `arm64-v8a` |



## 核心特性



### Flutter 双交互界面



BiliFlora 的页面同时考虑电视和手机。TV 模式使用明确的焦点边界、选中态和遥控器确认操作；手机模式使用卡片点击、触摸滚动、播放器控制按钮和手势入口。两种交互共用页面状态和业务接口。



### Rust 弹幕与字幕内核



Rust 内核覆盖传统 Bilibili XML 弹幕的 `p` 属性解析、文本与 CDATA 解码、秒到毫秒的精确换算、模式/字号/颜色/池/发送者哈希/行 ID 字段提取，以及按时间和稳定 ID 排序。规范化阶段会清理空文本和 BOM、限制异常字号、统一颜色范围，并对相同时间、文本和发送者的重复弹幕去重。字幕 JSON 使用独立模型，另提供时间窗口筛选和关键词屏蔽接口。



### 播放与内容浏览



迁移范围覆盖首页视频列表、搜索入口、视频详情、分 P/合集选择、播放器控制、弹幕/字幕开关、播放历史和设置页。原 `bv` 中已经验证过的 Android TV 功能将按优先级逐步接入，而不是一次性引入移动端的全部功能。



## 技术架构



```text

Flutter UI

  ├── TV focus navigation / mobile touch interaction

  ├── pages, player controls, settings

  └── Dart FFI adapter

          │ UTF-8 bytes + JSON + explicit lengths

          ▼

Rust Core (biliflora_core)

  ├── Bilibili data models and request pipeline

  ├── danmaku XML parser and timeline normalization

  ├── subtitle JSON parser

  ├── filtering, deduplication, cache and history

  └── stable C ABI for Android

          ├── armeabi-v7a (32-bit)

          └── arm64-v8a (64-bit)

```



## 项目结构



| 路径 | 说明 |

| --- | --- |

| `biliflora_flutter/` | Flutter UI 原型、TV 焦点导航、手机点击入口和 FFI 适配层 |

| `rust/` | Rust workspace、核心 crate、弹幕/字幕解析与 Android ABI 构建矩阵 |

| `BILIFLORA_MIGRATION.md` | 迁移边界、分层职责和 ABI 约定 |

| `app/` | 原 Android/Kotlin 应用基线，迁移期间用于功能对照 |

| `bili-api/`、`bili-api-grpc/` | 原项目 API 与 protobuf 基线，逐步迁入 Rust |

| `bv-player/` | 原项目播放器基线，逐步由 Flutter 播放器适配层承接 |



## 本地开发



### Rust 内核测试



```bash

cd rust

cargo fmt -- --check

cargo test --all-targets

```



当前测试覆盖 XML 弹幕排序、HTML 实体解码、字幕 JSON 时间转换、异常字号规范化、重复弹幕去重和屏蔽词过滤。



### Flutter UI



需要安装 Flutter SDK、Android SDK 和对应 Android NDK。进入 Flutter 工程后执行：



```bash

cd biliflora_flutter

flutter pub get

flutter test

flutter run

```



ABI 原生库按照 `rust/android-abi.toml` 中的矩阵构建后放入对应的 `jniLibs/<abi>/` 目录。



## 开发进度



| 模块 | 状态 |

| --- | --- |

| Flutter 首页、导航、视频卡片与播放器壳 | 已建立原型 |

| TV 焦点与手机点击入口 | 已建立原型 |

| Rust XML 弹幕解析 | 已实现并通过单元测试 |

| Rust 字幕 JSON 解析 | 已实现并通过单元测试 |

| Dart FFI 内存释放边界 | 已建立 |

| Flutter 播放器与 Rust 数据管线联调 | 进行中 |

| Android 32/64 位原生库构建 | 待 Android NDK 环境 |

| API、登录、缓存和完整播放器迁移 | 进行中 |



## 免责声明



BiliFlora 是个人学习与测试性质的开源重构项目，不提供任何破解内容，也不代表 Bilibili 官方。项目使用的网络接口、媒体内容和账号能力均受相关服务条款、所在地法律法规以及上游项目许可证约束。请勿将本项目用于违反法律法规、绕过访问控制或损害他人权益的用途。



> **再次提醒：BiliFlora 面向中国大陆以外用户使用。中国大陆地区用户请使用哔哩哔哩官方客户端或官方提供的云视听小电视。**
> 


## 致谢



本项目感谢 [Frost819/bv](https://github.com/Frost819/bv) 提供 Android TV 功能基线，感谢 [bggRGjQaUbCoE/PiliPlus](https://github.com/bggRGjQaUbCoE/PiliPlus) 提供 Flutter 客户端参考，也感谢 [bilibili-API-collect](https://github.com/SocialSisterYi/bilibili-API-collect)、[media-kit](https://github.com/media-kit/media-kit)、[Dio](https://pub.dev/packages/dio) 以及所有相关开源项目的贡献者。



## License



本项目沿用原仓库的 MIT License，详见 [LICENSE](LICENSE)。使用第三方依赖时请同时遵守其各自许可证。



