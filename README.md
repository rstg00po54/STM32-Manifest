# STM32-Manifest

使用 Google [repo](https://gerrit.googlesource.com/git-repo/) 工具统一管理 STM32 开发板资料与工程。每块板卡是一个独立的 Git 仓库，本仓库只维护 `default.xml`，不会合并它们的提交历史。

## 受管理的仓库

| 本地目录 | GitHub 仓库 | 分组 |
| --- | --- | --- |
| `boards/F429-FANKE` | `rstg00po54/STM32-Board-F429-FANKE` | `stm32f4` |
| `boards/H723-WeAct` | `rstg00po54/STM32-Board-H723-WeAct` | `stm32h7` |
| `boards/H750-WeAct` | `rstg00po54/STM32-Board-H750-WeAct` | `stm32h7` |
| `boards/H743-FANKE-M2` | `rstg00po54/STM32-Board-H743-FANKE-M2` | `stm32h7` |

以上板卡仓库目前均为私有仓库，需要具有访问权限的 GitHub 账号和 SSH Key 才能同步。

## Ubuntu / WSL 安装

```bash
sudo apt update
sudo apt install -y git repo
ssh -T git@github.com
```

第一次使用 SSH 时，需要先在 GitHub 账号中配置 SSH 公钥。`ssh -T` 成功认证时会显示 GitHub 欢迎信息，不提供 shell 访问是正常现象。

## 初始化与下载全部板卡

```bash
mkdir -p ~/STM32-Workspace
cd ~/STM32-Workspace

repo init -u git@github.com:rstg00po54/STM32-Manifest.git -b main
repo sync -j4
```

目录结构：

```text
STM32-Workspace/
├── .repo/
└── boards/
    ├── F429-FANKE/
    ├── H723-WeAct/
    ├── H750-WeAct/
    └── H743-FANKE-M2/
```

仓库包含较大的开发板资料，请预留足够磁盘空间，并保持网络连接稳定。

## 只下载 H7 系列（新工作区）

```bash
mkdir -p ~/STM32-H7-Workspace
cd ~/STM32-H7-Workspace

repo init -u git@github.com:rstg00po54/STM32-Manifest.git -b main -g stm32h7
repo sync -j4
```

## 常用命令

```bash
repo list                          # 列出受管理仓库
repo status                        # 查看本地修改
repo sync -j4                      # 同步全部选中的仓库
repo sync boards/H750-WeAct        # 只同步 H750
repo forall -c 'git log -1 --oneline'  # 查看每个仓库的最新提交
```

## 修改并推送单个项目

```bash
cd boards/H750-WeAct
git status
git add .
git commit -m "Update H750 files"
git push origin main
```

注意：`repo sync` **不会**自动提交或推送本地修改。同步前应先处理未提交的工作，以免更新发生冲突。要增加新的板卡仓库，只需修改本仓库的 `default.xml`，然后在工作区运行 `repo sync`。

## Manifest

入口文件：[`default.xml`](default.xml)。

所有项目当前均跟踪各自的 `main` 分支，因此每次同步会获取其最新提交；如需可复现版本，应把每个项目的 `revision` 固定到具体 commit SHA。
