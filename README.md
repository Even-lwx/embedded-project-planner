# Embedded Project Planner

一个面向嵌入式开发的 Codex Skills 仓库，提供项目规划、DCDC 电源原理图确认与 PCB 布局辅助能力。

## 主要特性

### 嵌入式项目规划助手

- 先确认原理图、芯片型号、网络和引脚连接，再查阅相关数据手册与协议。
- 识别 Keil、IAR、STM32CubeMX 及其他已有工程结构。
- 关键结论引用原理图页、器件位号、网络名、文档页码或源码行号。
- 无法解析文件或发现冲突时停止猜测，向用户索取可验证资料。
- `pcb/` 和 `doc/` 始终只读；首次调用不修改工程。
- 获批实施后，先建立 Git 与 `.gitignore` 基线，再在 `code/code/Hardware/` 中实现底层 GPIO、UART、SPI、USB、RS485、CAN 等实际需要的驱动，最后实现上层功能。

### DCDC 原理图与 PCB 布局助手

- 支持 Buck、Boost、Buck-Boost 等开关电源拓扑。
- 根据芯片型号和数据手册核对外围无源器件、反馈/补偿网络、耐压、纹波、电流、温升和封装。
- 先输出可确认的原理图、计算依据、BOM 和待确认项；用户确认后才进入 PCB 布局阶段。
- 按高 `di/dt` 回路、SW 节点、输入/输出回路、反馈回路、地回流、过孔、散热和 EMI 约束给出布局建议。
- 输出 SVG、HTML 或 PNG 等直观的原理图和 PCB 布局可视化结果。

## 安装

在 Codex 中发送：

```text
请安装这个 Skill：
https://github.com/Even-lwx/embedded-project-planner/tree/main/skills/embedded-project-planner
```

也可以把 `skills/embedded-project-planner` 目录复制到 `$CODEX_HOME/skills/embedded-project-planner`。

安装 DCDC 原理图与 PCB 布局助手：

```text
请安装这个 Skill：
https://github.com/Even-lwx/embedded-project-planner/tree/main/skills/dcdc-schematic-pcb-layout
```

也可以把 `skills/dcdc-schematic-pcb-layout` 目录复制到 `$CODEX_HOME/skills/dcdc-schematic-pcb-layout`。

## 使用

### 嵌入式项目规划

```text
使用 $embedded-project-planner
项目根路径：D:\projects\demo
目标功能：实现传感器采集驱动并接入现有任务调度
```

首次调用只会调查项目并制定计划。要进入实施阶段，需在计划完成后明确说明“按该计划实施”或同等含义。

### DCDC 电源设计

```text
使用 $dcdc-schematic-pcb-layout
芯片型号：MP1584EN
输入：18–24 V，左侧进入
输出：5 V / 2 A，上侧引出
请先查阅数据手册并输出外围原理图、器件额定值/封装和 BOM，等待我确认后再做 PCB 布局。
```

原理图确认后，再提供板框尺寸、层数、铜厚、安装孔、器件库格式和 EMI/散热约束，助手会输出关键环路、器件位置、过孔和走线方向的可视化布局建议。

## 仓库结构

```text
skills/
├── embedded-project-planner/
│   ├── SKILL.md
│   └── agents/openai.yaml
└── dcdc-schematic-pcb-layout/
    ├── SKILL.md
    ├── agents/openai.yaml
    └── references/design-checklist.md
```

## License

[MIT](LICENSE)
