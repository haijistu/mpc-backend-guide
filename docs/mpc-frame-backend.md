# mpc-frame 后端指南（用户版）

## 概览

把基于 `mpc-frame` 模板开发的 RTL 设计（以 `counter32` 为例），通过 `ecos-studio` 在本地跑完从 **综合(Synthesis)** 到 **硬化(Harden)** 的后端流程，导出 **Signoff Package**，再提交到 **ECOSFactory 云平台**并完成流片下单。

### 整体流程
#### 前端
1. 获取 `mpc-frame` 精简用户版模板
2. 根据 `mpc-frame` 项目README进行RTL设计和仿真测试
#### 后端
1. 安装并启动 `ECOS-studio`
2. 在 Resource Manager 下载资源：`MPC`、`PDK`、`Yosys`
3. 创建项目与工作区，配置工作区
4. 运行从 **综合(Synthesis)** 到 **硬化(Harden)** 共 12 步后端流程
5. 导出 **Signoff Package**，解压得到 DEF 与 RTL 网表
#### 云平台
1. 在 ECOSFactory 云端提交设计（DEF + RTL）
2. 流片下单

| 阶段 | 使用工具 | 主要产物 |
| --- | --- | --- |
| 前端 | `mpc-frame` | RTL设计源代码 |
| 后端 | `ecos-studio` | 综合网表 / DEF / Signoff Package |
| 云平台 | ECOSFactory | 设计提交记录 / Shuttle 订单 |

### 术语速查

| 术语 | 说明 |
| --- | --- |
| `mpc-frame` | 基于 `ecos-frame` 的 MPC 设计模板框架 |
| `ecos-studio` | 本地可视化后端流程工具，负责跑流程与导出产物 |
| ECOSFactory | 云端设计提交与流片下单平台 |
| PDK | 工艺库，本文使用 `ics55`（ICsprout 55nm） |
| Yosys | 逻辑综合工具，后端流程依赖 |
| Signoff Package | 后端流程完成后的交付包，含 DEF、网表、配置与报告 |


### 快速检查清单

#### 一、环境准备

- 已获取 `mpc-frame` 精简用户版模板
- 已下载 `ECOS-Studio` ，可成功启动并看到主页
- Resource Manager 中已下载下列资源
  - `MPC: mpc-frame`
  - `PDKs: ICsprout 55nm PDK`
  - `EDA Tools: Yosys`

#### 二、创建项目与工作区

- 已创建项目与 workspace（指定项目目录、工作区名称与路径）
- `New workspace` 向导已依次完成 Flow Setup / Design Files / PDK Config / Spec Setting
  - Design Files 选择 `mpc-frame/designs/<your_design>/rtl` 并配置 SDC
  - PDK Config 使用默认 `ics55`，Spec Setting 填写时钟、Die 面积、利用率、扇出等参数

#### 三、运行流程与导出

- `Flow status` 中 12 个流程全部跑完，时序报告无 Setup / Hold 违例
  - 若存在违例：优化设计或降低目标频率后重跑
- 已通过 File → `Export Signoff Package` 导出并解压，确认 `final/design/` 下 `counter32.def.gz` 与 `counter32.v.gz` 存在

#### 四、云端提交与下单

- ECOSFactory 已注册并登录，已提交设计（作品名称 + DEF + RTL）
- 已在 `流片下单` 中选择 `MPC-Frame` 及已提交的设计（或上传新设计）
- 已勾选 `工程预审`、`后端设计支持`、`开源优惠` 三项服务
- 已填写个人信息，核对无误后下单

## mpc-frame获取与使用
`mpc-frame` User Kit精简用户版的获取与使用方式见 [User Kit 指南](https://github.com/openecos-projects/mpc-frame/blob/release/user-kit/docs/cn/user-kit.md)。

在后端流程前，需要根据 README 对用户设计进行充分的仿真测试，确保功能正确性。后端（`ecos-studio`）只负责把 RTL 变成版图与交付包 (signoff package)，**不做功能验证**。

因此进入后端流程前，前端应当能够通过`user-lint`, `user-test`, `user-frame-test`测试，否则时序与面积优化都会建立在错误的功能之上。

以 `counter32` 为例，前端收尾命令如下：
```sh
make check DESIGN=designs/counter32
```

只有以上检查全部通过后，才进入 `ecos-studio` 后端流程，并在后续的 `Design Files` 中选择 `designs/<name>/rtl`。

## ecos-studio获取与使用
下载并运行 `ECOS-studio` : 以 `v0.1.0-alpha.7` 版本为例, 其余版本见 [ECOS-studio发行版](https://github.com/openecos-projects/ecos-studio/releases/)

```bash
wget https://github.com/openecos-projects/ecos-studio/releases/download/v0.1.0-alpha.7/ECOS-Studio_0.1.0-alpha.7_x86_64.AppImage
chmod +x ECOS-Studio_0.1.0-alpha.7_x86_64.AppImage
./ECOS-Studio_0.1.0-alpha.7_x86_64.AppImage
```

如果遇到报错`dlopen(): error loading libfuse.so.2`, 安装 libfuse2 库即可

```sh
sudo apt install libfuse2
```

启动成功后，将会看到以下窗口

![ecos-studio-HOME](../ScreenShots/ecos-studio-HOME.png)
### 下载资源
点击`Recourse Manager`进入资源管理器, 在左侧一栏下载相应资源
- `MPC`: `mpc-frame`
- `PDKs`: `ICsprout 55nm PDK`
- `EDA Tools`: `Yosys`

![MPC-frame-resource.png](../ScreenShots/MPC-frame-resource.png)

![Resource-Manager](../ScreenShots/Resource-Manager.png)

![Yosys](../ScreenShots/Yosys.png)

下载 `Yosys` 如遇以下问题：

```log
ERROR [resources] Failed to install tool:yosys: Archive contains unsupported link entry
```

可以从[镜像站](https://cnb.cool/ecoslab/oss-cad-suite-build/-/releases)下载最新版本的压缩包

![Yosys-import0](../ScreenShots/Yosys-import0.png)

将复制的下载链接拷贝到终端运行并解压后得到oss-cad-suite文件夹
```bash
# 以版本 2026-09-10 为例
wget "https://cnb.cool/ecoslab/oss-cad-suite-build/-/releases/download/2026-09-10/oss-cad-suite-linux-x64-20260910.tgz"
tar xvf oss-cad-suite-linux-x64-20260910.tgz
```

返回资源管理器，导入 oss-cad-suite 文件夹

![Yosys-import1](../ScreenShots/Yosys-import1.png)
### 创建新项目
基于`ecos-frame`创建，回到主页，进入Project Management

![Project-Management](../ScreenShots/Project-Management.png)

点击`New project`创建新的项目，以 `counter32` 为例

![new-project](../ScreenShots/new-project.png)

点击`New workspace`创建新的工作区

![new-workspace](../ScreenShots/new-workspace.png)

- `Project Setup` - 选择已有项目目录或创建新的项目目录
- `Basic Info` - 设置工作区名称并指定工作区路径
- `Flow Setup` - 从综合到硬化, 选择固定的硬化流程范围, 默认即可
- `Design Files` - 提供用于综合启动的RTL/文件列表, 或用于综合后启动的DEF加Verilog网表, 在此处配置SDC。对于`mpc-frame`设计的项目, 仅需选择"mpc-frame/designs/your_design/rtl", 系统会自动识别RTL代码

![design-files-rtl](../ScreenShots/design-files-rtl.png)

- `PDK Config` - 使用ECC默认的PDK配置, 或手动选择工艺LEF、单元LEF和Liberty文件。pdk选择默认的ics55

![pdk-config](../ScreenShots/pdk-config.png)

- `Spec Setting` - 配置设计、时钟、Die面积、利用率、扇出及相关参数

![spec-setting](../ScreenShots/spec-setting.png)

### 运行后端流程
进入工作区后, 点击右上角区域`Flow status`的启动按钮, 自动运行从`synthesis`到`Harden`共12个流程

![Flow-status](../ScreenShots/Flow-status.png)

### 导出signoff Package
完成后点击左上角File -> `Export Signoff Package` 导出signoff package,

![file_signoff](../ScreenShots/file_signoff.png)

![export_signoff](../ScreenShots/export_signoff.png)

常见问题：存在时序违例，需要优化设计或降低目标频率

![Setup_Violation](../ScreenShots/Setup_Violation.png)

解压signoff_package, 目录结构如下(仅展示部分内容)

```txt
.
├── README.md
├── config
├── final
│   ├── design
│   │   ├── counter32.def.gz (def文件, 解压后提交到云平台)
│   │   └── counter32.v.gz (RTL代码, 解压后提交到云平台)
├── harden
├── initial
├── manifest.json
├── summary.json
└── synthesis
```

### ECOSFactory云平台
注册账号后, 在主页点击提交设计

![ECOSFactory](../ScreenShots/ECOSFactory.png)

选择MPC-Frame

![Design-submission](../ScreenShots/Design-submission.png)

输入作品名称, 上传def文件和RTL源文件

![Submit-File](../ScreenShots/Submit-File.png)

设计提交完成后, 即可下单, 点击`Order Shuttle`

![Order_Shuttle](../ScreenShots/Order_Shuttle.png)

选择MPC-Frame

![Shuttle](../ScreenShots/Shuttle.png)

选择你提交的设计或者上传新设计

![Shuttle-design](../ScreenShots/Shuttle-design.png)

选择`工程预审`, `后端设计支持`, `开源优惠`三项服务

![shuttle-services](../ScreenShots/shuttle-services.png)

填写个人信息

![personal_info](../ScreenShots/personal_info.png)

仔细比对信息是否正确，无误后下单

![place_order](../ScreenShots/place_order.png)