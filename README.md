# Adaptive Channel Scanning with Multi-Armed Bandits

A simulation study of adaptive wireless-channel scanning using a multi-armed bandit (MAB).

The project models channels with different temporal coherence properties and investigates whether an adaptive scanner can allocate sensing time more effectively than a fixed-dwell strategy.

## Main idea

A wireless scanner cannot continuously observe every channel. It therefore has to decide which channel to scan next, and for how long.
This project models each channel as an arm in a multi-armed bandit. Channels that have not been observed recently receive a larger reward, while channels with shorter coherence times are treated as needing more frequent updates.

The experiment compares the resulting adaptive sampling pattern against a naive fixed-dwell strategy.

## Repository structure

```text
├── 01_coherence_mab.ipynb
├── 02_wifi_signal_mab_experiment.ipynb
└── README.md
```

**`01_coherence_mab.ipynb`** is the main experiment.

It contains the complete simulation pipeline:

1. Generate correlated I/Q channel signals.
2. Estimate channel coherence time using autocorrelation.
3. Perform an initial scan and maintain DFS-style channel states.
4. Determine channel dwell times from coherence estimates.
5. Select channels using an epsilon-greedy MAB policy.
6. Reconstruct the sparsely observed signals.
7. Generate a naive fixed-dwell baseline.
8. Estimate channel power.
9. Compare adaptive scanning, naive scanning, and ground truth.

### Wi-Fi experiment

**`02_wifi_signal_mab_experiment.ipynb`** explores the same general problem using recorded complex IQ data and STFT-based processing.

It is kept separate because it depends on external `.dat` files that are not included in this repository.

## Method

### Channel model

Each channel contains I/Q signals generated using an Ornstein–Uhlenbeck process, providing controllable temporal correlation.
Different correlation parameters produce channels with different coherence times.

### Coherence estimation

The notebook estimates coherence using the signal's autocorrelation:

```text
channel signal
      ↓
remove mean
      ↓
FFT-based autocorrelation
      ↓
smooth / normalize
      ↓
coherence-time estimate
```

### Multi-armed bandit

Each channel is treated as an arm.

The reward is based on how long it has been since the channel was last observed relative to its estimated coherence time:

```text
reward ∝ time since last observation / coherence time
```

An epsilon-greedy policy balances exploration and exploitation:

- with probability ε, explore a channel
- otherwise, select the channel with the highest reward

### Baseline

A naive scanner uses a fixed dwell duration for each channel rather than adapting its sensing schedule to channel dynamics. The reconstructed signals are compared with the full-resolution ground truth.

This work has been done as part of the mid-term submission for the Arista Networks PS in Inter-IIT Tech Meet 2025.