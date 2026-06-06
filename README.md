# se3-yolo

基于 Ultralytics YOLO 的 RoboMaster 目标检测模型训练仓库。

## 环境配置

```bash
uv sync
```

## 项目结构

```
se3-yolo/
├── configs/
│   ├── armor/
│   │   └── data.yaml
│   ├── energy_mechanism/
│   │   └── data.yaml
│   └── robot/
│       └── data.yaml
├── datasets/
│   ├── armor/
│   │   ├── images/
│   │   │   ├── train/
│   │   │   └── val/
│   │   └── labels/
│   │       ├── train/
│   │       └── val/
│   ├── energy_mechanism/
│   │   ├── images/
│   │   │   ├── train/
│   │   │   └── val/
│   │   └── labels/
│   │       ├── train/
│   │       └── val/
│   └── robot/
│       ├── images/
│       │   ├── train/
│       │   └── val/
│       └── labels/
│           ├── train/
│           └── val/
├── runs/
│   ├── armor/
│   ├── energy_mechanism/
│   └── robot/
├── pyproject.toml
└── README.md
```

每个目标（armor / energy_mechanism / robot）都有独立的配置文件、数据集和训练输出目录。

## 数据集

标注文件采用 YOLO 检测格式：

```
class_id x_center y_center width height
```

所有框坐标已归一化到 `0..1` 范围。

数据集配置文件位于 `configs/<目标>/data.yaml`。

## 训练

保持数据集配置不变，通过切换 `model` 和 `name` 来训练不同规模的模型：

```bash
uv run yolo detect train model=yolo26n.pt data=configs/robot/data.yaml project=runs/robot name=yolo26n
uv run yolo detect train model=yolo26s.pt data=configs/robot/data.yaml project=runs/robot name=yolo26s
uv run yolo detect train model=yolo26m.pt data=configs/robot/data.yaml project=runs/robot name=yolo26m
```

`armor` 和 `energy_mechanism` 同理，替换对应路径即可。

## 验证

```bash
uv run yolo detect val model=runs/robot/yolo26n/weights/best.pt data=configs/robot/data.yaml
```

## 预测

```bash
uv run yolo detect predict model=runs/robot/yolo26n/weights/best.pt source=datasets/robot/images/val
```

## 环境检查

```bash
uv run yolo checks
```
