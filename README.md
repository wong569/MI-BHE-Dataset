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
- **参考电极**: [根据实际情况填写]
- **接地电极**: [根据实际情况填写]

### 电极布局
```
        Fz
    F3      F4
    
        Cz
    C3      C4
    
        
    P3      P4
```

## 被试信息

| 被试ID | 姓名 | 实验编号 | 实验次数 |
|--------|------|----------|----------|
| 01 | 郑凯 | 011, 012, 013 | 3 |
| 02 | 王睿 | 021, 022, 023 | 3 |
| 03 | 李懿琛 | 031, 032, 033 | 3 |

- **总被试数**: 3人
- **每人实验次数**: 3次
- **每次实验trials**: 200个
- **总trials数**: 1800个

## 文件结构

```
BCI-Motor-Imagery-Dataset/
│
├── README.md                          # 本说明文件
├── introduction.txt                   # 实验信息和裁剪参数
├── sync_eeg_data.py                   # 数据同步处理脚本
│
├── raw_data/                          # 原始数据目录
│   ├── 011.csv                        # 被试1-实验1 EEG数据
│   ├── BCI_Experiment_011.csv         # 被试1-实验1 标签数据
│   ├── 012.csv
│   ├── BCI_Experiment_012.csv
│   ├── ...
│   ├── 033.csv
│   └── BCI_Experiment_033.csv
│
└── processed_data/                    # 处理后数据目录
    ├── processed_011.npz              # 处理后的实验数据
    ├── processed_012.npz
    ├── ...
    ├── processed_033.npz
    └── dataset_summary.txt            # 数据集摘要
```

## 数据格式说明

### 原始EEG数据文件 (XXX.csv)
- **格式**: CSV文件
- **列数**: 8列，对应8个EEG通道（F3, F4, Fz, C3, C4, Cz, P3, P4）
- **采样率**: 250 Hz
- **单位**: 微伏（μV）

### 标签文件 (BCI_Experiment_XXX.csv)
包含每个trial的详细信息：
- `trial_number`: Trial编号（1-200）
- `direction`: 运动想象方向（中文：左手/右手/双脚/舌头）
- `direction_code`: 方向代码（left/right/feet/tongue）
- `start_time`: Trial开始时间戳
- `end_time`: Trial结束时间戳
- `duration`: Trial持续时间（秒）

### 处理后数据文件 (processed_XXX.npz)
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

## 数据同步说明

由于EEG采集时间与实验程序运行时间不完全同步，原始EEG数据需要裁剪才能与标签对齐。裁剪参数记录在`introduction.txt`文件中。

### 裁剪参数示例
```
实验ID  前裁剪(秒)  后裁剪(秒)
011     9          7
012     9          9
013     7          7
...
```

## 使用方法

### 1. 环境要求
```bash
pip install numpy pandas
```

### 2. 数据同步处理
运行提供的Python脚本进行数据同步：

```python
python sync_eeg_data.py
```

脚本会自动：
1. 读取`introduction.txt`获取裁剪参数
2. 裁剪原始EEG数据
3. 将EEG数据与标签对齐
4. 保存为处理后的npz格式

### 3. 加载处理后的数据
```python
import numpy as np

# 加载数据
data = np.load('processed_data/processed_011.npz', allow_pickle=True)

# 提取数据
eeg_data = data['data']        # (200, ~2312, 8) - 200个trials
labels = data['labels']         # (200,) - 标签
channels = data['channel_names']  # 通道名称
sampling_rate = data['sampling_rate']  # 250 Hz

print(f"数据形状: {eeg_data.shape}")
print(f"标签形状: {labels.shape}")
print(f"标签分布: {np.bincount(labels)}")
```

### 4. 数据预处理示例
```python
import numpy as np
from scipy import signal

def bandpass_filter(data, lowcut=8, highcut=30, fs=250, order=4):
    """
    带通滤波器（8-30 Hz，适用于运动想象）
    """
    nyq = 0.5 * fs
    low = lowcut / nyq
    high = highcut / nyq
    b, a = signal.butter(order, [low, high], btype='band')
    return signal.filtfilt(b, a, data, axis=0)

# 加载数据
data = np.load('processed_data/processed_011.npz', allow_pickle=True)
eeg_data = data['data']

# 对每个trial进行滤波
filtered_data = []
for trial in eeg_data:
    filtered_trial = bandpass_filter(trial)
    filtered_data.append(filtered_trial)
filtered_data = np.array(filtered_data)

print(f"滤波后数据形状: {filtered_data.shape}")
```

## 数据质量

- ✅ 所有数据均经过质量检查
- ✅ 标签与EEG数据精确对齐
- ✅ 每个类别的trial数量平衡
- ⚠️ 部分trial可能包含眼动或肌电伪迹，建议使用时进行伪迹去除

## 应用场景

1. **BCI算法开发**: 训练和测试运动想象分类算法
2. **深度学习研究**: 开发基于深度学习的BCI解码模型
3. **特征提取**: 研究运动想象的脑电特征
4. **迁移学习**: 跨被试的BCI模型迁移研究
5. **实时BCI系统**: 开发在线BCI解码系统

## 引用

如果您使用了本数据集，请引用：

```bibtex
@dataset{bci_motor_imagery_2025,
  title={BCI Motor Imagery Dataset - Four-Class Motor Imagery EEG Data},
  author={郑凯, 王睿, 李懿琛},
  year={2025},
  publisher={GitHub},
  url={https://github.com/[your-username]/BCI-Motor-Imagery-Dataset}
}
```

## 许可证

本数据集采用 [MIT License / CC BY 4.0 / 其他] 许可证发布。

## 联系方式

如有问题或建议，请通过以下方式联系：
- GitHub Issues: [项目链接]
- Email: [您的邮箱]

## 致谢

感谢所有参与数据采集的被试。

## 更新日志

- **2025-01**: 初始版本发布
  - 3名被试，每人3次实验
  - 共1800个trials的四分类运动想象数据

---

**关键词**: BCI, Motor Imagery, EEG, Brain-Computer Interface, Machine Learning, Deep Learning, 运动想象, 脑机接口
