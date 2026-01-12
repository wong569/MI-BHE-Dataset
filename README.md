# BCI Motor Imagery Dataset

## Dataset Overview

This is a four-class motor imagery brain-computer interface (BCI) dataset containing electroencephalogram (EEG) signals from 3 healthy subjects. The dataset adopts a standard motor imagery paradigm and can be used for BCI algorithm research, machine learning model training, and neuroscience research.

## Experimental Design

### Motor Imagery Tasks
- **Classification Categories**: Four classes
  - Left Hand Motor Imagery
  - Right Hand Motor Imagery
  - Both Feet Motor Imagery
  - Tongue Motor Imagery

### Trial Temporal Structure
Each trial contains the following stages with a total duration of approximately 9.25 seconds:
1. **Fixation**: 2 seconds - Subject fixates on a cross at the center of the screen
2. **Cue**: 1.25 seconds - Display of motor imagery task prompt
3. **Execution**: 4 seconds - Subject performs motor imagery
4. **Rest**: 2 seconds - Relaxation in preparation for the next trial

```
[Fixation 2s] → [Cue 1.25s] → [Execution 4s] → [Rest 2s]
       ↓              ↓              ↓            ↓
    Prepare      Task Prompt    Motor Imagery   Relax
```

## Data Acquisition Parameters

### Hardware Configuration
- **Sampling Rate**: 250 Hz
- **Number of Channels**: 8
- **Electrode Positions**: F3, F4, Fz, C3, C4, Cz, P3, P4 (International 10-20 system)

### Electrode Layout
```
        Fz
    F3      F4
    
        Cz
    C3      C4
    
        
    P3      P4
```

## Subject Information

- **Total Number of Subjects**: 3
- **Experiments per Subject**: 3 sessions
- **Trials per Experiment**: 200
- **Total Number of Trials**: 1,800

## Data Format Description

NumPy compressed format containing:
- `data`: EEG data array with shape (n_trials, n_samples, n_channels)
- `labels`: Label array with shape (n_trials,)
  - 0: Left hand
  - 1: Right hand
  - 2: Both feet
  - 3: Tongue
- `channel_names`: List of channel names
- `sampling_rate`: Sampling rate
- `experiment_id`: Experiment ID

## Usage

### 1. Environment Requirements
```bash
pip install numpy pandas
```

### 2. Loading Data
```python
import numpy as np

# Load data
data = np.load('processed_011.npz', allow_pickle=True)

# Extract data
eeg_data = data['data']              # (200, ~2312, 8) - 200 trials
labels = data['labels']               # (200,) - labels
channels = data['channel_names']      # channel names
sampling_rate = data['sampling_rate'] # 250 Hz

print(f"Data shape: {eeg_data.shape}")
print(f"Labels shape: {labels.shape}")
print(f"Label distribution: {np.bincount(labels)}")
```

## Citation

If you use this dataset, please cite:

```bibtex
@dataset{bci_motor_imagery_2025,
  title={Low-Cost EEG Acquisition and HankelDMD–AGRU for Motor Imagery Robotic Control},
  author={Su Wang},
  year={2025},
  publisher={GitHub},
  url={https://github.com/wong569/MI-BHE-Dataset}
}
```

## License

This dataset is released under the [MIT License / CC BY 4.0 / Other] license.

## Acknowledgments

We thank all subjects who participated in the data collection.
