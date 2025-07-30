# CHIME/FRB Detection Pipeline Overview

The CHIME/FRB detection pipeline processes data in multiple stages (L0–L4), enabling real-time discovery of Fast Radio Bursts (FRBs). Below is a breakdown of each level in the pipeline.

*Based on readings from Ketan's, Ziggy's, and Pragya’s thesis chapters 2 & 3.*  
[Link to source](https://escholarship.mcgill.ca/concern/theses/db78tj079)

---

## CHIME (Before CHIME/FRB System)

CHIME is a radio interferometer with the following characteristics:

- Field of view: ~250 square degrees
- Location: Penticton, British Columbia, Dominion Radio Astrophysical Observatory (DRAO)
- Frequency range: 400–800 MHz (corresponds to redshifts 0.8–2.5 of the 21 cm H line)

### Antennas

- 256 antennas per cylindrical reflector → 1024 total
- Reflectors: 100 m × 20 m, aligned North–South
- Cloverleaf feed design across 80 m focal line, spaced by 30.48 cm
- Focal length: 5 m → causes a 30 MHz ripple from reflections ("metal bar thing")
- Each feed has 2 orthogonal linear polarizations →  
  256 feeds × 4 cylinders × 2 polarizations = **2048 signal inputs**

### Analog Signal Chain

- Feed → LNA → 400–800 MHz bandpass filter  
  → transmitted to 2 receiver huts (between cylinders) via coaxial cables

### Digitizers

- Located in receiver huts
- Implemented using 128 ICE boards (FPGA + ADC)
- 8-bit precision; sampling rate: 800 MSPS (νₛ = 800 MHz)
- Samples from the 2nd Nyquist zone: νₛ/2 to νₛ  
  **Question:** _What exactly does this imply? Why is the Nyquist channel discarded?_

### FX Engine

#### F-Engine (Fourier Engine)

- Performs channelization into 1024 frequency bins using a polyphase filter bank (PFB)
- PFB = FFT + window function → minimizes spectral leakage
- Adds per-channel gain and phase offsets
- Output rounded to complex 4+4 bits (real + imag) → **reduces data rate to 6.5 Tb/s**
- Resolution:
  - Frequency: 390 kHz
  - Time: 2.56 µs

#### X-Engine (Cross-correlator)

- **L0 stage** of CHIME/FRB
- Receives 2048 inputs per node, grouped in 4 frequency channels
- Forms visibilities and beams

### Calibration

- Beamforming: Corrects geometric + instrumental delays
- Complex gain: $G = A e^{-i\phi}$
  - Determined daily via bright sources: Cas A, Cyg A, Vir A, Tau A
- Calibration depends on season and source visibility
- Stored gains used for baseband analysis
- **Question:** _Is this performed before L0?_

---

## L0 Stage – Beamforming & Upchannelization

- **L0 = X-engine**
- Responsible for beamforming and frequency upchannelization

### Beamforming

- 256 GPU nodes process 2048 inputs (from 4 frequency channels each)
- Frequency channels separated by 100 MHz (redundancy in case of node failure)
- FFT-based beamforming:
  - 512 beams in N–S direction → beam centers clamped across frequencies
  - Spacing uniform in sin(θ) from −60° to +60° zenith angle
  - E–W direction: formed using **exact phasing**  
    **Question:** _What is "exact phasing" in this context?_
  - Beam FWHM:
    - 0.25° at 800 MHz
    - 0.5° at 400 MHz
  - Incoherent beam: √N lower sensitivity → useful for RFI monitoring
- CHIME/Pulsar: Uses 10 beams at 2.56 µs time resolution

> **TODO:** Add Fig. 2.2 from Ketan's thesis

### Upchannelization

- Mitigates intra-channel DM smearing
- 384 voltage samples @ 2.56 µs → FFT applied
- Final resolution:
  - Time: 0.983 ms
  - Frequency: 24.4 kHz (6384 channels)
- Two polarizations summed → Stokes I (8-bit integers)

---

## L1 – Single Beam Search

L1 handles:

- RFI mitigation
- Dedispersion
- Real-time single beam search
- Intensity ring buffers for storage

### Processing

- 128 L1 nodes → each handles 8 adjacent beams (total 1024)
- Uses a telescoping ring buffer → downsampled over time

### RFI Mitigation

- Real-time C++/assembly pipeline (trained with CHIME pathfinder data)

### Dedispersion Search

- Blind DM search:
  - Naive: \( O(N_t N_\nu N_{\text{DM}}) \)
  - Bonsai tree: \( O(N_t N_\nu \log_2 N_{\text{DM}}) \)
    - Handles widths up to ~60 ms
    - Spectral indices: −3, 0, +3
    - DM range: 0–13000 pc/cm³
- Output: 4D S/N array (DM, time, spectral index, width)
  - Coarse-grained in DM/time
  - >8σ triggers → **L1 events**

### Metadata

- Header includes: S/N, DM, arrival time, beam, etc.
- RFI grade based on DM vs S/N
- 4 L1 nodes dedicated to incoherent beams (0000, 1000, 2000, 3000)  
  → used only for RFI, not FRB detection

---

## L2 – Beam Event Grouping

- All L1 events sent to a single node

### Grouping

- Events grouped across beams using **DBSCAN** clustering
- Results in an **L2 event**

### Machine Learning Classifier

- Inputs: L1 RFI grade, beam S/N, DM, activity
- Output: Score 0 (RFI) → 10 (likely FRB)

### Localization

- For multi-beam events: S/N distribution + beam model + spectral indices
- Uses a precomputed lookup table

---

## L3 – Decision Making

- Also handled by L2 node
- Determines Galactic vs extragalactic

### Logic

- Cross-reference with known sources: pulsars, RRATs, FRBs via `KnownSourceSifter`
- Compare DM to NE2001 and YMW16 predictions
  - Classify: Galactic / Extragalactic / Ambiguous

### Triggers

- Triggers L1 ring buffer to write data
- Triggers baseband storage (if S/N high)
- Sends VOEvent alerts

---

## L4 – Callbacks and Storage

- Stores metadata + pointers to data products
- Built on:
  - MariaDB 3 (relational database)
  - Django (web interface)

### Final Classification

- Human review using **Intensity-ML** (CNN-based)
- Events flagged as RFI by 2 reviewers deleted after 24 hours

### Offline Analysis

- Refines:
  - Localization
  - DM
  - Fluence
  - Burst morphology

- Executes L3-determined callbacks:
  - Save intensity data from L1
  - Save baseband data from L0

---
