# Open-source Synthetic Electrochemistry Datasets

这是 CA、CP、CV 合成电化学数据的标准化开源副本。原始目录
`CA_synthetic_dataset/`、`CP_synthetic_dataset/` 和 `cv_dataset_grid/`
没有被移动或改写。

> 当前状态：预发布。数据已完成数量与校验和验证，但尚未选择开源许可证。

## 数据规模

| 数据集 | 标准曲线 | 原始风格曲线 | 元数据 | 补充数量 |
| --- | ---: | ---: | ---: | ---: |
| CA | 40 个 CSV | — | 40 条 | 10 |
| CP | 40 个 CSV | — | 40 条 | 0 |
| CV | 405 个 CSV | 405 个 TXT | 405 条 | 210 |

当前目录约 38 MB。所有 `dataset_id` 和文件名在各自数据集中唯一。

## 目录结构

```text
open_source_synthetic_datasets/
├── CA/
│   ├── curves/          # 40 条标准化 CA 曲线
│   └── metadata.jsonl
├── CP/
│   ├── curves/          # 40 条标准化 CP 曲线
│   └── metadata.jsonl
├── CV/
│   ├── curves/          # 405 条标准 CV 曲线
│   ├── raw/             # 405 条 CHI 风格带干扰曲线
│   └── metadata.jsonl
└── README.md
```

## 统一字段

### CA 与 CP

| 字段 | 含义 | 单位 |
| --- | --- | --- |
| `time_s` | 时间 | s |
| `potential_V` | 电位 | V |
| `current_A` | 电流 | A |

### CV 标准曲线

| 字段 | 含义 | 单位/取值 |
| --- | --- | --- |
| `voltage_V` | 扫描电位 | V |
| `current_mA` | 电流 | mA |
| `time_s` | 扫描时间 | s |
| `scan_direction` | 扫描方向 | `1` 正扫，`-1` 反扫 |

CV 的 `raw/*.txt` 使用 `Potential/V, Current/A`，保留基线漂移、模拟工频干扰或
两者叠加的合成原始信号。每个 TXT 只包含 1000 个数值点，扫速头已按对应元数据修正。


## 元数据

每个 `metadata.jsonl` 文件一行对应一条标准曲线。通用字段包括：

- `dataset_id`：数据集内唯一 ID；
- `file_name`：相对于对应 CA、CP 或 CV 目录的曲线路径；
- `source_file`：原仓库文件路径；新增样本为 `null`；
- `source_group` 或 `physical_truth_status`：数据来源和标签可靠性；
- `n_points`、物理参数或工况字段：不同实验类型的专属信息。

CV 另外包含 `raw_file`、`condition`、`raw_type`、`ground_truth`、
`empirical_features` 和 `llm_base_params`。旧的 15 条无索引曲线中，无法恢复的字段为
`null`，其经验极值由标准曲线重新计算。

## 读取示例

```python
from pathlib import Path
import json
import pandas as pd

root = Path("open_source_synthetic_datasets")

ca = pd.read_csv(next((root / "CA/curves").glob("*.csv")))
cp = pd.read_csv(next((root / "CP/curves").glob("*.csv")))
cv = pd.read_csv(next((root / "CV/curves").glob("*.csv")))

with (root / "CV/metadata.jsonl").open(encoding="utf-8") as handle:
    cv_metadata = [json.loads(line) for line in handle if line.strip()]
```

文件名包含空格、括号和 `µ`，建议使用 `pathlib.Path`，并始终以 UTF-8 读取元数据。


## 使用与划分建议

- 数据全部为合成数据，不应被描述为真实仪器实验结果；
- CV 可按材料或完整工况分组划分，以评估对未见材料和工况的泛化能力。

## 正式发布前

- [ ] 选择数据许可证并添加 `LICENSE`；
- [ ] 添加作者、机构、联系方式、版本号和发布日期；

