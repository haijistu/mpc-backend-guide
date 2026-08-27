# mpc-frame 后端指南（用户版）

本文档介绍了基于`mpc-frame`框架开发的DESIGN（以counter32为例）, 如何通过`ecos-studio`完成后端流程, 并下单到`ecos-factory`云平台

## mpc-frame获取与使用
`mpc-frame` User Kit精简发行版的获取与使用方式见`mpc-frame`下的`User Kit 指南`

## ecos-studio获取与使用
下载并运行`ECOS-studio`: 以`v0.1.0-alpha.7`版本为例, 将`<latest-release-file>`替换为`ECOS-Studio_0.1.0-alpha.7_x86_64`, 其余版本见https://github.com/openecos-projects/ecos-studio/releases/

```sh
wget https://github.com/openecos-projects/ecos-studio/releases/latest/download/<latest-release-file>.AppImage
chmod +x <latest-release-file>.AppImage
./<latest-release-file>.AppImage
```
如果遇到报错`dlopen(): error loading libfuse.so.2`, 安装 libfuse2 库即可
```sh
sudo apt install libfuse2
```
![ecos-studio-HOME](../ScreenShots/ecos-studio-HOME.png)

### 下载资源
点击`Recourse Manager`进入资源管理器, 在左侧一栏下载相应资源
- `MPC`: `mpc-frame`
- `PDKs`: `ics55`

![MPC-frame-resource.png](../ScreenShots/MPC-frame-resource.png)

![Resource-Manager](../ScreenShots/Resource-Manager.png)

### 创建新项目
基于`ecos-frame`创建，回到Project Management

![Project-Management](../ScreenShots/Project-Management.png)

点击`New project`创建新的项目

![new-project](../ScreenShots/new-project.png)

点击`New workspace`创建新的工作区
- `Project Setup` - 选择已有项目目录或创建新的项目目录
- `Basic Info` - 设置工作区名称并指定工作区路径
- `Flow Setup` - 从综合到硬化, 选择固定的硬化流程范围, 默认即可
- `Design Files` - 提供用于综合启动的RTL/文件列表, 或用于综合后启动的DEF加Verilog网表, 在此处配置SDC
- `PDK Config` - 使用ECC默认的PDK配置, 或手动选择工艺LEF、单元LEF和Liberty文件
- `Spec Setting` - 配置设计、时钟、Die面积、利用率、扇出及相关参数

![new-workspace](../ScreenShots/new-workspace.png)

对于`mpc-frame`设计的项目, 选择"mpc-frame/designs/your_design/rtl", 系统会自动识别RTL代码

![design-files-rtl](../ScreenShots/design-files-rtl.png)

pdk选择默认的ics55

![pdk-config](../ScreenShots/pdk-config.png)

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
注册账号后, 在主页点击Submit Design -> 选择MPC-Frame -> Continue, 输入名称后, 上传def文件和RTL源文件, 点击Submit提交

![ECOSFactory](../ScreenShots/ECOSFactory.png)

![Design-submission](../ScreenShots/Design-submission.png)

![Submit-File](../ScreenShots/Submit-File.png)

设计提交完成后, 即可下单, 点击`Order Shuttle`

![Order_Shuttle](../ScreenShots/Order_Shuttle.png)

选择MPC-Frame -> 点击Continue -> 选择你提交的设计或者上传新设计 -> 选择`Engineering review assist`, `Backend support`, `Open-source incentive`三项服务 -> 填写个人信息 -> 确认下单

![Shuttle](../ScreenShots/Shuttle.png)

![shuttle-services](../ScreenShots/shuttle-services.png)

![personal_info](../ScreenShots/personal_info.png)

![place order](../ScreenShots/place_order.png)