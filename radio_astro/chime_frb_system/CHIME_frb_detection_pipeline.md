# CHIME/FRB Detection Pipeline Overview

The CHIME/FRB detection pipeline processes data in multiple stages (L0–L4), enabling real-time discovery of Fast Radio Bursts (FRBs). Below is a breakdown of each level in the pipeline.

*Based on readings from Ketan's thesis chapter 2, Ziggy's thesis chapters 2 & 3 [link to source](https://escholarship.mcgill.ca/concern/theses/f4752n54q), and Pragya’s thesis chapters 2 & 3.*  [Link to source](https://escholarship.mcgill.ca/concern/theses/db78tj079)

On Ziggy Chapter 3

---

## CHIME (Before CHIME/FRB System)

CHIME is a radio interferometer with the following characteristics:

- Field of view: ~250 square degrees
-  4 Reflectors: 100 m × 20 m, aligned North–South
- Location: Penticton, British Columbia, Dominion Radio Astrophysical Observatory (DRAO)
	-  This observatory is located in a natural valley where it is shielded from radio frequency interference (RFI) by a mountain range. 
	- The area is furthermore deemed a protected area by the government which prohibits the use of any electronic devices.
- Frequency range: 400–800 MHz (corresponds to redshifts 0.8–2.5 of the 21 cm H line)
	- originally meant for mapping out the large-scale structure and constraining dark energy’s equation of state. 
- Total collecting area of 8,000 m$^2$ ~ 8 Hockey Rinks 

> **TODO:** Add Fig. 2.3 from Ziggy's thesis

### Cylinders
* 4 Reflectors: 100 m × 20 m, aligned North–South
* Cylinders are covered with a mesh with 19 mm spacing and a 2.2 mm wire gauge. The spacing is sufficiently dense to reflect 400–800 MHz radio waves
* design is parabolical in the east-west directions, limiting the FoV in that direction to 1.3–2.6◦ (800–400 MHz), and open in the north-south direction for a horizon-to-horizon FoV in that direction

### Antennas
* Antennas are also known as feeds
* less than 10 dB return loss over the 400–800 MHz bandwidth
* reflection coefficient or return loss, S11. In case S11 = 0 dB, all incoming power is reflected from the antenna. S11 = −10 dB implies that 90% of the power is transferred into the feed
- 256 antennas per cylindrical reflector → 1024 total
- 4 Reflectors: 100 m × 20 m, aligned North–South
- Cloverleaf feed design across 80 m focal line, spaced by 30.48 cm
	- feeds are installed along the central 78 m beneath a walkway 
- Focal length: 5 m → causes a 30 MHz ripple from reflections ("metal bar thing")
- Each feed has 2 orthogonal linear polarizations →  
  256 feeds × 4 cylinders × 2 polarizations = **2048 signal inputs**

### Analog Signal Chain

- Feed → LNA → 400–800 MHz bandpass filter  
  → transmitted to 2 receiver huts (between cylinders) via  (~128 km of) coaxial cables
- LNAs with less than 35 K noise across most of the band
- 

### Digitizers

- Located in receiver huts, ∼128 km of coaxial cables
- Apart of the F-engine 
- Implemented using 128 ICE boards (FPGA + ADC, and  Fourier-transformed)
- 8-bit precision; sampling rate: 800 MSPS (νₛ = 800 MHz)
- Rate for digitization by the F-engine of 13.1 Tb/ s
- Samples from the 2nd Nyquist zone: νₛ/2 to νₛ  
  **Question:** _What exactly does this imply? Why is the Nyquist channel discarded?_

### FX Engine

#### F-Engine (Fourier Engine)
* Inputs are 2x1024 feeds (2 polarizations)
* on the ICE boards
- Performs channelization into 1024 frequency bins using a polyphase filter bank (PFB)
- PFB = FFT + window function → minimizes spectral leakage
- Adds per-channel gain and phase offsets
- Output rounded to complex 4+4 bits (real + imag) → **reduces data rate to 6.5 Tb/s**
- Resolution:
  - Frequency: 390 kHz
  - Time: 2.56 µs
* The F-engine output is routed with ∼80 km of optical fiber to the GPU huts.
* Final step: rearranges data such that every GPU node of the X-engine receives all 2,048 signals from 4 frequency channels for spatial correlation.

#### X-Engine (Cross-correlator)

- **L0 stage** of CHIME/FRB
- Receives 2048 inputs per node, grouped in 4 frequency channels
- Forms visibilities and beams

### Calibration

- Beamforming: Corrects geometric + instrumental (sometimes stochastic, in the analog and digital signal chain) delays
- Complex gain: $G = A e^{-i\phi}$
  - Determined daily via bright sources: Cas A, Cyg A, Vir A, Tau A
- Calibration source depends on season and source visibility
- Stored gains used for baseband analysis
- In case the calibrator source is not Cyg A, the gain solution is multiplied by a frequency- and feed-dependent beam ratio such that the calibration is always normalized to Cyg A.
- Calibrations are calculated separately for each feed, each frequency channel and each polarization.
- There is a ∼30-MHz ripple in the amplitude gain due to a standing wave between the focal line and the receiver. The phase offset of this ripple is dependent on declination.

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
- Outputs for CHIME/FRB experiment are 1,024 total intensity beams, in 16,384 frequency channels with a 0.98304-ms sampling time in 8-bit format. At a rate of 142 Gbps, corresponding to 1.5 PB per day.

> **TODO:** Add Fig. 2.2 from Ketan's thesis

### Upchannelization

- Mitigates intra-channel DM smearing
- 384 voltage samples @ 2.56 µs → FFT applied
- Final resolution:
  - Time: 0.983 ms
  - Frequency: 24.4 kHz (6384 channels), but ziggy's thesis says  16,384 frequency channels 
- Two polarizations summed → Stokes I (8-bit integers)

---

## L1 – Single Beam Search

L1 handles:

- RFI mitigation
- Dedispersion
- Real-time single beam search
- Intensity ring buffers for storage

### Processing

- 128 L1 CPU nodes → each handles 8 adjacent beams (total 1024)
- Uses a telescoping ring buffer → downsampled over time
- One L1 node consists of two CPU processors, RAM, dual socket motherboard, power supply, case cooling including cooling block and loop, radiator and fans

### RFI Mitigation

- Real-time C++/assembly pipeline (trained with CHIME pathfinder data)

### Dedispersion Search

- Blind DM search:
  - Naive: $O(N_t N_\nu N_{\text{DM}})$
  - Bonsai tree: $O(N_t N_\nu \log_2 N_{\text{DM}})$
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

- All L1 events sent to a single node (shared with L3)
-  to avoid any one-point failures, the node is duplicated

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

- Shares the L2 node
- to avoid any one-point failures, the node is duplicated
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

- The L4 machine is located not in the FRB sea-container but in the VSOP room near the main DRAO building. It is connected to the FRB sea-can with a 300-m fibre using TCP as a communication protocol.
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
