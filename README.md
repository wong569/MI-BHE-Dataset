# BCI Motor Imagery Dataset

## 数据集简介

本数据集是一个四分类运动想象脑机接口（BCI）数据集，包含3名健康被试的脑电（EEG）信号数据。数据集采用标准的运动想象范式，可用于BCI算法研究、机器学习模型训练和神经科学研究。

## 实验设计

### 运动想象任务
- **分类类别**: 四分类
  - 左手运动想象（Left Hand）
  - 右手运动想象（Right Hand）
  - 双脚运动想象（Both Feet）
  - 舌头运动想象（Tongue）

### Trial时序结构
每个trial包含以下阶段，总时长约9.25秒：
1. **注视期** (Fixation): 2秒 - 被试注视屏幕中央的十字
2. **提示期** (Cue): 1.25秒 - 显示运动想象任务提示
3. **执行期** (Execution): 4秒 - 被试执行运动想象
4. **休息期** (Rest): 2秒 - 放松准备下一个trial

```
[注视 2s] → [提示 1.25s] → [执行 4s] → [休息 2s]
    ↓            ↓            ↓           ↓
   准备        任务提示    运动想象      放松
```

## 数据采集参数

### 硬件配置
- **采样率**: 250 Hz
- **通道数**: 8
- **电极位置**: F3, F4, Fz, C3, C4, Cz, P3, P4（国际10-20系统）

### 电极布局
```
        Fz
    F3      F4
    
        Cz
    C3      C4
    
        
    P3      P4
```

## 被试信息

- **总被试数**: 3人
- **每人实验次数**: 3次
- **每次实验trials**: 200个
- **总trials数**: 1800个


## 数据格式说明

NumPy压缩格式，包含：
- `data`: EEG数据数组，形状为 (n_trials, n_samples, n_channels)
- `labels`: 标签数组，形状为 (n_trials,)
  - 0: 左手
  - 1: 右手
  - 2: 双脚
  - 3: 舌头
- `channel_names`: 通道名称列表
- `sampling_rate`: 采样率
- `experiment_id`: 实验ID


## 使用方法

### 1. 环境要求
```bash
pip install numpy pandas
```

### 2. 加载数据
```python
import numpy as np

# 加载数据
data = np.load('processed_011.npz', allow_pickle=True)

# 提取数据
eeg_data = data['data']        # (200, ~2312, 8) - 200个trials
labels = data['labels']         # (200,) - 标签
channels = data['channel_names']  # 通道名称
sampling_rate = data['sampling_rate']  # 250 Hz

print(f"数据形状: {eeg_data.shape}")
print(f"标签形状: {labels.shape}")
print(f"标签分布: {np.bincount(labels)}")
```

## 引用

如果您使用了本数据集，请引用：

```bibtex
@dataset{bci_motor_imagery_2025,
  title={Low-Cost EEG Acquisition and HankelDMD–AGRU for Motor Imagery Robotic Control},
  author={Su Wang},
  year={2025},
  publisher={GitHub},
  url={https://github.com/wong569/MI-BHE-Dataset}
}
```

## 许可证

本数据集采用 [MIT License / CC BY 4.0 / 其他] 许可证发布。


## 致谢

感谢所有参与数据采集的被试。
