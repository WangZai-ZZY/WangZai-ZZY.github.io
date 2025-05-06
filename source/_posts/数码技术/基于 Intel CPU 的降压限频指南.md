---
title: 基于 Intel CPU 的降压限频指南
date: 2023/2/5 12:00:00
author: 汪崽zzy
categories: 数码技术
cover: [https://s2.loli.net/2023/02/05/XEh9vrifQjAbDkl.jpg]
banner: 
  type: img
  bgurl: https://s2.loli.net/2023/02/05/XEh9vrifQjAbDkl.jpg
---

> 通过降低 CPU 功耗，来降低整机功耗，延长笔记本续航时间，顺带提高性能释放的“性价比”。

# 软件选择
1. [XTU（Inter® Extreme Tuning Utility）](https://www.intel.cn/content/www/cn/zh/download/17881/29183/intel-extreme-tuning-utility-intel-xtu.html)
- XTU 只对部分 CPU 解锁了全部功能，具体可在下载页面查看 CPU 支持情况。
- 简要结论：
  - 后缀为 X、XE、K、KF、KS、HK 的 i3/i5/i7/i9 CPU 受支持。
  - 移动端 CPU 与低压 CPU 均不支持，即后缀为 H、U 等。
2. [ThrottleStop](https://www.techpowerup.com/download/techpowerup-throttlestop/)
- 无法使用 XTU 时，可以尝试使用 ThrottleStop 替代。
- 本教程使用该方案。

# ThrottleStop 界面介绍
<img src="https://s2.loli.net/2023/02/05/vK9bLgoy8X37Y2S.png" alt="image-20230205102157963" style="zoom: 75%;" />

## 主界面 - 左侧
<img src="https://s2.loli.net/2023/02/05/DU4ympMhRIFzeTu.png" alt="image-20230205115706894" style="zoom: 75%;" />

在最上面的红框中有四个单选项：选择配置文件，默认有4个，分别是 Performance、Game、Internet、Battery。<br><br>

而下面红框中的设置项与上面所选定的配置文件相对应：
- **Clock Mod（时钟调制）**：【已过时，无需设置】
- **Set Multiplier（倍频设置）**：【已过时，无需设置】
- **Speed Shift EPP（动态调频设置）**：【从 Skylake 架构（第六代酷睿）开始支持，可酌情设置】
  - Intel® Speed Shift Technology，即硬件自主根据提示工作量快速变化频率以获得最优能耗。调整其 EPP 参数可以影响 CPU 对任务的响应性能。比如当 CPU 利用率较低时，通过提高 CPU 主频，可以缩短任务运行时间。
  - EPP 允许设置范围为 0~255 ：0 表示 CPU 更倾向于工作在最大睿频（性能），255 表示 CPU 更倾向于工作在较低频率（节能）。
    - 插电/希望获得最大性能：推荐设置在 0~32 之间。
    - 不插电/希望延长电池使用时间：至少设置为 128。
- **Power Saver（省电器）**：【已过时，无需设置】
- **Disable Turbo（禁止睿频）**：【一般情况下无需设置】
  - 关闭 Turbo Boost，CPU 将保持在基础频率工作。
- **SpeedStep**：【Skylake 架构（第六代酷睿）之前的 CPU 支持，之后的无需设置】
  - 通过软件对 CPU 进行动态频率调整，已被基于硬件的 Speed Shift 替代。
- **BD PROCHOT（Bi-directional Processor Hot，双向处理器温控）**：【可酌情设置】
  - 开启时，当 CPU 或 GPU 任一个的温度超过设定阈值时，另一个无论处在何种温度下都会被降频处理，以控制设备的整体温度。
  - 一般来说是笔记本在玩游戏时 CPU 低温降频的元凶。
- **C1E（C1E enhanced halt stat，增强型空闲电源管理状态转换）**：【玄学，一般情况下无需设置】
  - 关闭时，可以防止 CPU 睿频机制自动关闭某些核心。
- **Task Bar（最小化到任务栏）**：阻止 ThrottleStop 最小化到托盘，而是将其保留在任务栏中。
- **On Top（窗口置顶）**
- **Log File（启用日志）**
- **More Data（记录更多数据）**：加快右侧表格数据更新频率，由每秒记录1次数据提高至每秒记录8次数据。

最下面的三个按钮：Save（将当前设置存入 .ini 文件），Options（进入软件设置菜单），Turn On/Off（已经没什么用了，只要软件在运行设置便会生效，想要暂停生效/重置需要删除路径下的 .ini 文件并关闭电源，最好也可以重置一下 BIOS）。

## 主界面 - 右侧
<img src="https://s2.loli.net/2023/02/05/cYFMotQ1uWslgJx.png" alt="image-20230205120708702" style="zoom: 75%;" />

在最上面的红框中显示的是 CPU 当前的电压、倍频、外频与核心频率，点击该区域可以使软件窗口最小化至托盘或任务栏。<br><br>

而下面的表格展现的是当前 CPU 各个线程（超线程选项打开）/内核（超线程关闭）的相关数据：
- **FID**：当前线程/内核的实时倍频，通常等于 CPU 当前频率除以 FSB 时钟。
- **C0%**：当前线程/内核处在其最高性能状态（C0）下的时间百分比，空闲时该数值较低，高负载时数值较高。
- **Mod**：参考左侧配置中的 Clock Mod 选项，在现代 CPU 中应为100%。
- **℃**：当前线程/内核的实时温度。
- **MAX**：当前线程/内核的最高温度。

至于下面就是 CPU 各项数据的平均值，其中 **PKG/Max Power** 是指当前/最大记录到的功率。<br><br>

再往下的几个按钮：
- **Limits**：显示 CPU CORE、GPU、Ring 的降频原因。
- **FIVR**：即 Fully Integrated Voltage Regulator，完全集成电压调节器，用于调整与 CPU 电压相关的内容。
- **TPL**：即 Turbo Power Limit，用于调整与睿频相关的内容。
- **CLR**：清零上面表格中的 MAX 记录值。
最下面就是 TS Bench（内置的基准测试程序，可检测在软件中的设置将对 CPU 产生怎样影响）、Batt （显示当前电池点亮）、GPU （显示当前 GPU 温度）。

## 子界面 - Options
<img src="https://s2.loli.net/2023/02/05/zXejBl8uPZ13KTv.png" alt="image-20230205134606799" style="zoom: 75%;" />

## 子界面 - FIVR
<img src="https://s2.loli.net/2023/02/05/reWqLvOpo5Unhya.png" alt="image-20230205140806331" style="zoom: 75%;" />

此页面一般只需要关注中间这列的 CPU Core 和 CPU Cache 这俩常用设置项。<br><br>

调整电压时，需要同时调整 Adaptive 选项下的 CPU Core 和 CPU Cache 的 Offset Voltage 值。至于降多少合适，可以自己一点点降进行测试（如果某次蓝屏了说明降得多了），也可以在网上找现成的数值。

## 子界面 - TPL
<img src="https://s2.loli.net/2023/02/05/npiA85h3HI1GvUu.png" alt="image-20230205140830555" style="zoom: 75%;" />

此页面一般只需要关注中间那个红框（Power Limit Controls）中的内容即可。
- **Long Power PL1**：长时睿频功耗，允许 CPU 长时间运行的最大功耗，通常设定为 CPU 的 TDP。
- **Short Power PL2**：短时睿频功耗，允许 CPU 在某段时间内可以达到的最大功耗上限。
- **Lock**：在寄存器中将设置锁定，避免软件或系统对其进行修改。如果日后要解锁需要将该软件重置。
- **Clamp**：勾选代表任何时候都遵循此设置，不勾选时仅在睿频时有效。例如 CPU 默频 3.4G，但是在 3.4G 时仍然超过所给功率限制（例如做密集 AVX 运算），勾选 Clamp 的情况会强制 CPU 继续降频直到满足功耗限制，不勾选的话则不会。

# 参考链接
1. [The ThrottleStop Guide (2023): How to Lower Temperatures, Increase Performance and Boost Battery Life](https://www.ultrabookreview.com/31385-the-throttlestop-guide)
2. [降频？不存在的，来看看Throttlestop](https://zhuanlan.zhihu.com/p/415865127)
3. [xtu不能用？用游戏本神器 ThrottleStop 参数设置对 Intel 游戏本帧数优化教程，以9750h为例](https://www.bilibili.com/read/cv12252445)

# 扩展阅读
1. [被误解的CPU利用率、超线程、动态调频 —— CPU 性能之迷 Part 1](https://blog.mygraphql.com/zh/notes/hw/hyper-threading/)
1. [笔记本中常说的PL1、PL2到底如何影响CPU性能？](https://www.expreview.com/71943.html)