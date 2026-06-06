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
│   │   ├── data.yaml           # 训练数据集配置
│   │   └── export.yaml         # ONNX 导出配置
│   ├── energy_mechanism/
│   │   └── ...
│   └── robot/
│       └── ...
├── datasets/
│   ├── armor/
│   │   ├── images/
│   │   │   ├── train/
│   │   │   └── val/
│   │   └── labels/
│   │       ├── train/
│   │       └── val/
│   ├── energy_mechanism/
│   │   └── ...
│   └── robot/
│       └── ...
├── runs/
│   ├── armor/
│   ├── energy_mechanism/
│   └── robot/
├── prek.toml                   # pre-commit hooks
├── pyproject.toml
└── README.md
```

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

## 导出 ONNX

训练完成后，使用 export 配置文件导出 ONNX 模型用于推理部署：

```bash
yolo export cfg=configs/<target>/export.yaml
```

导出配置（`configs/<target>/export.yaml`）：

```yaml
model: runs/armor/weights/best.pt
format: onnx
imgsz: 640
simplify: true
opset: 12
```

生成的 `.onnx` 文件可直接用 ONNX Runtime 加载推理。

## 环境检查

```bash
uv run yolo checks
```
