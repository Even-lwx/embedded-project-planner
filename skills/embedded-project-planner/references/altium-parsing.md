# Altium/AD 文件解析参考流程

本参考用于规划阶段读取 Altium Designer 原理图和 PCB 文件。它不授权修改 `pcb/` 或 `doc/`。

## 1. 文件与工具探测

先识别 `.SchDoc`、`.PcbDoc`、`.PrjPcb`、`.PrjPcbStructure`、ECO 日志、BOM、网表和 PDF。检查本机已有的 Altium Viewer/Designer、KiCad CLI、7-Zip，以及 Python/Node 运行时。工具选择应以当前文件的实际解析结果为准。

可评估的开源实现：

- [Altium-Schematic-Parser](https://github.com/a3ng7n/Altium-Schematic-Parser)：将 `.SchDoc` 转为 JSON，适合提取器件与原理图记录。
- [altium.js](https://github.com/gsuberland/altium_js)：JavaScript 解析和渲染 `.SchDoc`，适合第二解析器或可视交叉检查。
- Python `olefile`：读取 OLE/复合文档容器，适合作为解析器失败时的底层入口。

第三方包可能存在版本缺陷；安装或导入成功不等于解析成功。必须使用项目中的真实文件做最小读取测试。

## 2. `.SchDoc` 的 OLE 兜底路径

当 `.SchDoc` 是 OLE/复合文档时：

1. 以只读方式列出 OLE 流和存储。
2. 优先读取 `FileHeader` 流。
3. 检查是否包含 `HEADER=Protel for Windows - Schematic Capture Binary File Version 5.0` 和 `RECORD=` 记录。
4. 解析 `RECORD=1` 的元件主体、`RECORD=2` 的引脚、`RECORD=34/41` 的位号和属性、以及网络标签/导线记录。
5. 通过 `OwnerIndex`、`OwnerPartId`、`UniqueID` 等关联元件、引脚和属性。
6. 输出临时 JSON/CSV/文本报告，记录源文件路径和解析器版本；不要把生成物写入 `pcb/`。

只提取到器件属性不能证明网络连接。网络必须由原始连接记录或独立证据确认。

## 3. 原理图连通性图（必经阶段）

连通性解析必须在固件引脚规划前完成。建立图时：

1. 为每个元件和每个引脚建立节点，保留位号、完整料号、引脚号、引脚名、`OwnerIndex` 和坐标。
2. 将导线端点、总线分支、网络标签、全局端口和电源端口转换为边或网络锚点；优先使用端点坐标和 `UniqueID`，不要按文本出现顺序猜测连接。
3. 对坐标相连的导线执行连通分量分析，再把同名网络标签/端口合并到同一网络。
4. 输出每个网络的成员：`元件位号.引脚号/引脚名`、网络名、来源记录和是否存在悬空端点。
5. 对 MCU、充电芯片、电流检测放大器、显示器和连接器生成专项映射报告；至少包含控制输入、状态输出、模拟输出、SPI 信号、电源和地。
6. 将“未连接”“只有单端导线”“同名网络冲突”“多驱动冲突”和“无法关联的坐标”单独列为错误或待确认项。

连通性报告的最低验收标准：每个目标器件的关键引脚都有网络状态；每个 MCU 外设信号都能追溯到原理图记录；关键网络至少由一项独立证据交叉验证。报告未达到此标准时，计划只能停留在调查阶段，不得生成具体 GPIO/ADC/SPI 配置。

## 4. `.PcbDoc` 与连接关系验证

对 `.PcbDoc` 使用专用 PCB 解析器或 Altium 导出物；若只能读取 ECO 日志，日志只能作为变更证据，不能替代完整网表。应优先获取 Altium 导出的网表、BOM、PDF 或 ASCII 文本格式，并将器件位号、网络名和封装与 `.SchDoc` 解析结果比对。

## 5. 证据记录格式

每条硬件结论至少记录：

- 源文件和相对路径；
- 解析器/工具及版本；
- 记录类型、`OwnerIndex`、`UniqueID` 或导出页码；
- 器件位号、完整料号、引脚和网络名；
- 独立交叉验证来源；
- 事实、推断或待确认状态。

无法关联到网络的器件或引脚必须列入风险与待确认项，不能用 MCU 命名或接口名称猜测连接。
