# Project Plan: Decoding Imagined Movement from EEG

**Goal:** Build an EEG decoder that predicts imagined movement (left vs. right hand) from brain signals, using public data. Understand every line of it. Use it to open real conversations with the head of data at Conduit Intelligence, then ask about a role in spring with months of visible work behind me.

**Dataset:** [PhysioNet EEG Motor Movement/Imagery (EEGMMIDB)](https://physionet.org/content/eegmmidb/1.0.0/): 109 subjects, 64 channels, 160 Hz. MNE can download it directly (`mne.datasets.eegbci`).
**Stretch dataset (only if ahead of schedule):** THINGS-EEG (decoding which image someone saw).

**Start:** Mon Oct 19, 2026 · **The ask:** first week of March 2027 · **Budget:** 6–8 hrs/week, in 2–3 fixed sessions

---

## The rules

1. **I write the code. Claude teaches.** Claude explains concepts, asks me questions, reviews my code, and gives hints. Claude only writes code when I explicitly say "show me", and then I retype it and explain it back.
2. **No line I can't explain.** If I can't say what a line does and why, it doesn't get committed.
3. **No skipping checkpoints.** A phase is done when I can pass its checkpoint *without notes*. Not before.
4. **Commit every week**, even if it's small. The GitHub history is part of the pitch.
5. **Learning log every session.** 3–5 lines in `LEARNING_LOG.md`: what I did, what confused me, what I'll do next.
6. **Behind schedule? Cut scope, don't skip understanding.** Drop stretch goals first, then later phases. Never drop a checkpoint.
7. **Friend contact every 3–4 weeks, never more.** One specific question or one result with a chart. Never just "hey, checking in".

---

## Phases

### Phase 0: Setup · Week 1 (Oct 19–25)
- [ ] Create a Python environment with `uv` (python 3.12, plus mne, numpy, scipy, matplotlib, scikit-learn, jupyter)
- [ ] Learn the git loop: `status → add → commit → push`. Make the first commit myself
- [ ] Download subject 1 with `mne.datasets.eegbci.load_data` and open it in MNE
- [ ] Read the dataset page: which runs are imagined vs. real movement? What do T0/T1/T2 mean?

**Checkpoint:** Explain what a `.edf` file contains, what 160 Hz sampling means, and which run numbers are left/right *imagined* hand movement.

### Phase 1: What EEG actually is · Weeks 2–3 (Oct 26–Nov 8)
- [ ] Plot raw signals with `raw.plot()`. Find an eye blink and a muscle artifact by eye
- [ ] Set the channel montage and plot sensor positions. Find C3, Cz, C4 and say why they matter
- [ ] Compute and plot the power spectrum (PSD). Find the alpha/mu (8–13 Hz) and beta (13–30 Hz) bands
- [ ] Concepts: sampling rate, Nyquist, frequency bands, reference electrode, volume conduction
- [ ] Notebook: `notebooks/01_explore_raw.ipynb`

**Checkpoint:** Explain what *event-related desynchronization (ERD)* is and why imagining a left-hand movement should change the mu rhythm over the *right* motor cortex (C4).

### Phase 2: Preprocessing · Weeks 4–5 (Nov 9–22)
- [ ] Band-pass filtering: what it does, why 7–30 Hz for motor imagery, what filter edge artifacts are
- [ ] Events → epochs: cut the signal into trials around each cue
- [ ] Re-referencing (average reference) and why it matters
- [ ] ICA to remove eye blinks. Look at the components and pick which to drop
- [ ] Plot ERD: time-frequency map of left vs. right trials at C3/C4. **I should see the effect myself before any ML**
- [ ] Notebook: `notebooks/02_preprocessing.ipynb`, plus reusable functions moved into `src/`

**Checkpoint:** Draw the full pipeline from raw file to epochs on paper, and explain why each step exists.
📨 **Friend contact #1 (~Nov 20):** a specific question, e.g. how they handle eye-blink/muscle artifacts in production (ICA vs. regression vs. just rejecting trials?).

### Buffer · Week 6 (Nov 23–29, Thanksgiving)
Catch up only. No new material.

### Phase 3: First decoder (replicate a known result) · Weeks 7–8 (Nov 30–Dec 13)
- [ ] Features first, the simple way: log band power at C3/C4 → logistic regression
- [ ] Then the classic way: **CSP + LDA** (Common Spatial Patterns + Linear Discriminant Analysis)
- [ ] Understand CSP: what spatial filters are, why it maximizes variance for one class vs. the other. Plot the CSP patterns
- [ ] Cross-validation done right: stratified k-fold, fitting CSP *inside* the fold (sklearn `Pipeline`)
- [ ] Know my chance level (50%, and why a small trial count makes "60%" less impressive than it sounds)
- [ ] Compare my numbers to published CSP results on this dataset
- [ ] Notebook: `notebooks/03_csp_lda.ipynb`

**Checkpoint:** Explain *data leakage* with a concrete example from this project (e.g., fitting ICA/CSP on all data before splitting) and show that my pipeline avoids it.

### Buffer · Weeks 9–10 (Dec 14–27, finals + holidays)
Light work only: clean up code, write README notes.
📨 **Friend contact #2 (~Dec 18):** first result + one chart. "Got X% on left vs. right imagined hand with CSP+LDA. Here's what I tried."

### Phase 4: Scale up and be rigorous · Weeks 11–13 (Dec 28–Jan 17)
- [ ] Run the pipeline on all 109 subjects. Plot the accuracy distribution (some people are "BCI-illiterate". Why?)
- [ ] Within-subject vs. **cross-subject** decoding (train on others, test on a new person). Explain why the second is much harder
- [ ] Permutation test: is my accuracy actually above chance?
- [ ] Refactor: scripts in `src/` that run end to end from one command
- [ ] ⚠️ **Early Jan: contact USC OISS about CPT / work authorization before any offer, paid or unpaid**

**Checkpoint:** Explain why cross-subject accuracy is lower and name 2 reasons from the data itself.

### Phase 5: Deep learning · Weeks 14–17 (Jan 18–Feb 14)
- [ ] PyTorch basics: tensors, a training loop, train/val/test splits
- [ ] Implement **EEGNet** myself, reading the paper alongside (Lawhern et al., 2018). Compare against `braindecode`'s version afterward
- [ ] Compare EEGNet vs. CSP+LDA honestly, same splits. It's fine if deep learning doesn't win. Explain why it might not
- [ ] Notebook: `notebooks/05_eegnet.ipynb`

**Checkpoint:** Explain what EEGNet's temporal convolution and depthwise spatial convolution each correspond to in terms of the "classic" pipeline (filtering, CSP).
📨 **Friend contact #3 (~Jan 22):** a question, e.g. how they evaluate models across people/sessions, or what their signal looks like vs. research EEG.

### Phase 6: Write-up · Weeks 18–19 (Feb 15–28)
- [ ] README as a real write-up: question, data, method, results, figures, what I learned, what I'd do next
- [ ] 3–4 clean figures (raw + artifacts, ERD map, CSP patterns, accuracy comparison)
- [ ] Repo cleanup: requirements, how to reproduce, no dead code
- [ ] Optional: short LinkedIn post with the main figure

📨 **Friend contact #4 (~Feb 26):** share the finished write-up.

### Phase 7: The ask · Week 20 (Mar 1–7)
- [ ] Ask, linking the repo, after OISS has confirmed what's allowed. Mention specifically what I'd want to work on and why it relates to what they do

---

## Stretch goals (only if a phase finishes early)
- Real vs. imagined movement: does a model trained on one transfer to the other?
- 4-class decoding (left hand / right hand / both fists / both feet)
- Riemannian geometry approach (`pyriemann`, tangent space + logistic regression), a strong classic baseline
- THINGS-EEG: decode visual object categories

## Progress log
| Week | Planned | Actually did | On track? |
|------|---------|--------------|-----------|
| 1    | Phase 0 |              |           |
