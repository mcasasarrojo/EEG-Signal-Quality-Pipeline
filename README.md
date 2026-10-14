[README.md](https://github.com/user-attachments/files/33428404/README.md)
# EEG Signal Quality Pipeline

Automated quality check for EEG recordings, written in Python with MNE-Python.

Before an EEG recording is used in a study, someone has to check that the signal is usable: no disconnected electrodes, no channels full of noise, and a reasonable number of artifacts. This project does that automatically and produces a quality report for each recording.

## What it does

1. **Loads public EEG data** from the [PhysioNet EEG Motor Movement/Imagery Dataset](https://physionet.org/content/eegmmidb/1.0.0/) (64 channels, 160 Hz): one minute of rest with eyes open and one with eyes closed, for 10 subjects.
2. **Preprocesses the signal**: notch filter at 60 Hz (power line, USA) and band-pass filter at 1-40 Hz.
3. **Scores every channel**:
   - flat channels (peak-to-peak amplitude close to zero)
   - noisy channels (high-frequency noise in the 20-40 Hz band, compared across channels with a robust z-score)
   - channels with abnormal power line noise
4. **Detects eye blinks** on the frontal channels with an adaptive threshold (median + k · MAD).
5. **Gives each recording a quality score** from 0 to 100.
6. **Checks a known physiological effect**: occipital alpha power (8-13 Hz) should rise when the eyes close (Berger effect).
7. **Writes an HTML report** per recording with the score, the flagged channels, the blink plot and a topographic noise map.
8. **Bonus**: a logistic regression that classifies eyes open vs eyes closed from band powers, validated on subjects it has never seen (GroupKFold).

## Results

Tested on 10 subjects (20 recordings, 1 minute each).

**Physiological check (alpha / Berger effect)**
- Occipital alpha power increased with eyes closed in **10 of 10 subjects** (Wilcoxon signed-rank test, **p = 0.002**).
- Median increase: **8.2x** (range 1.2x to 26.3x).

| Subject | Alpha eyes open (µV²) | Alpha eyes closed (µV²) | Ratio |
|---|---|---|---|
| 1 | 271.2 | 3481.7 | 12.8 |
| 2 | 116.4 | 1133.7 | 9.7 |
| 3 | 176.6 | 3023.5 | 17.1 |
| 4 | 17.5 | 462.0 | 26.3 |
| 5 | 24.6 | 33.2 | 1.4 |
| 6 | 16.5 | 19.3 | 1.2 |
| 7 | 518.7 | 1663.8 | 3.2 |
| 8 | 40.3 | 269.9 | 6.7 |
| 9 | 82.0 | 313.7 | 3.8 |
| 10 | 159.1 | 1906.6 | 12.0 |

**Quality scores**
- Mean score: **86.2 / 100** with eyes open and **91.3 / 100** with eyes closed.
- Blink detection behaves as expected: 7 to 37 blinks per minute with eyes open, against 0 to 6 per minute with eyes closed.
- Subject 6 had the lowest scores (60 and 67), with 11 to 12 channels flagged as noisy in both runs, which is the kind of recording the pipeline is meant to catch before analysis.

**Classifier (eyes open vs closed)**
- Accuracy on subjects never seen in training: **0.76 ± 0.10** (logistic regression, 5-fold GroupKFold by subject).
- Alpha power is the strongest feature (weight +2.2), as expected.

![Example report](images/report_example.png)

**Known limitation**: with eyes closed, very strong alpha can raise the 20-40 Hz noise metric on occipital channels and flag one of them (for example O2 in subject 1) as noisy. A next version could exclude alpha harmonics from the noise band.

## How to run

Open `eeg_signal_quality.ipynb` in Google Colab and run all cells. The data downloads automatically.

Reports are saved in the `reports/` folder, with a `summary.csv` for all recordings.

## Tools

Python, MNE-Python, NumPy, SciPy, pandas, scikit-learn, Matplotlib.

## Next steps

- Remove blinks with ICA instead of only detecting them.
- Sliding-window alpha index, the basis of alpha neurofeedback.

## Author

María Casás Arrojo, Biomedical Engineer
