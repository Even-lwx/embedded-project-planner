# Embedded Project Planner

一个面向嵌入式项目的 Codex Skill。用户只需提供项目根路径和目标功能，Agent 会依次调查 `pcb/`、`doc/`、`code/`，先产出带依据的实施计划；只有用户明确批准当前计划后，才允许修改 `code/`。

## 主要特性

- 先确认原理图、芯片型号、网络和引脚连接，再查阅相关数据手册与协议。
- 识别 Keil、IAR、STM32CubeMX 及其他已有工程结构。
- 关键结论引用原理图页、器件位号、网络名、文档页码或源码行号。
- 无法解析文件或发现冲突时停止猜测，向用户索取可验证资料。
- `pcb/` 和 `doc/` 始终只读；首次调用不修改工程。

## 安装

在 Codex 中发送：

```text
请安装这个 Skill：
https://github.com/Even-lwx/embedded-project-planner/tree/main/skills/embedded-project-planner
```

也可以把 `skills/embedded-project-planner` 目录复制到 `$CODEX_HOME/skills/embedded-project-planner`。`CODEX_HOME` 未设置时，默认位置通常是 `~/.codex/skills/embedded-project-planner`。

## 使用

```text
使用 $embedded-project-planner
项目根路径：D:\projects\demo
目标功能：实现传感器采集驱动并接入现有任务调度
```

首次调用只会调查项目并制定计划。要进入实施阶段，需在计划完成后明确说明“按该计划实施”或同等含义。

## 仓库结构

```text
skills/
└── embedded-project-planner/
    ├── SKILL.md
    └── agents/
        └── openai.yaml
```

## License

[MIT](LICENSE)
