# Decoding Imagined Movement from EEG

Work in progress: building a classifier that predicts whether a person is *imagining* moving their left or right hand, from 64-channel EEG.

- **Data:** [PhysioNet EEG Motor Movement/Imagery Dataset](https://physionet.org/content/eegmmidb/1.0.0/) (109 subjects)
- **Tools:** MNE-Python, scikit-learn, PyTorch
- **Approach:** preprocessing (filtering, ICA) → classic CSP + LDA baseline → EEGNet

See [PLAN.md](PLAN.md) for the roadmap and [LEARNING_LOG.md](LEARNING_LOG.md) for progress notes.
