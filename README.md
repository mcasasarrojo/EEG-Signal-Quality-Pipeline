Automated quality check for EEG recordings, written in Python with MNE-Python.

Before an EEG recording is used in a study, someone has to check that the signal is usable: no disconnected electrodes, no channels full of noise, and a reasonable number of artifacts. This project does that automatically and produces a quality report for each recording.

What it does
Loads public EEG data from the PhysioNet EEG Motor Movement/Imagery Dataset (64 channels, 160 Hz): one minute of rest with eyes open and one with eyes closed, for 10 subjects.
Preprocesses the signal: notch filter at 60 Hz (power line, USA) and band-pass filter at 1-40 Hz.
Scores every channel:
flat channels (peak-to-peak amplitude close to zero)
noisy channels (high-frequency noise in the 20-40 Hz band, compared across channels with a robust z-score)
channels with abnormal power line noise
Detects eye blinks on the frontal channels with an adaptive threshold (median + k · MAD).
Gives each recording a quality score from 0 to 100.
Checks a known physiological effect: occipital alpha power (8-13 Hz) should rise when the eyes close (Berger effect).
Writes an HTML report per recording with the score, the flagged channels, the blink plot and a topographic noise map.
Bonus: a logistic regression that classifies eyes open vs eyes closed from band powers, validated on subjects it has never seen (GroupKFold).
Results
<!-- Fill in after running the notebook -->
Alpha power increased with eyes closed in X of 10 subjects (Wilcoxon signed-rank test, p = X).
Eyes open vs closed classifier accuracy on unseen subjects: X ± X.

Mostrar imagen

How to run

Open eeg_signal_quality.ipynb in Google Colab and run all cells. The data downloads automatically.

Reports are saved in the reports/ folder, with a summary.csv for all recordings.

Tools

Python, MNE-Python, NumPy, SciPy, pandas, scikit-learn, Matplotlib.

Next steps
Remove blinks with ICA instead of only detecting them.
Sliding-window alpha index, the basis of alpha neurofeedback.
Author

María Casás Arrojo, Biomedical Engineer
