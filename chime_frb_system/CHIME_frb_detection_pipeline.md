
The CHIME/FRB detection pipeline processes data in multiple stages (L0–L4), enabling real-time discovery of Fast Radio Bursts (FRBs). Below is a breakdown of each level in the pipeline:

Based on reading Ketan's, Ziggy's and Pragya’s thesis chapters 2,3  [link]([https://escholarship.mcgill.ca/concern/theses/db78tj079](https://escholarship.mcgill.ca/concern/theses/db78tj079))

## CHIME (before the CHIME/FRB system)
* A radio interferometer 
- FOV is ~250 Square degrees
- CHIME is located in Penticton, British Columbia, within the Dominion Radio Astrophysical Observatory (DRAO) campus.
- 400-800MHz range => corresponding to redshifts between 0.8 and 2.5 of the 21 cm H line



* **Antennas:** 
	* 256 antennas per cylindrical reflectors for 1024 in total
	- Each reflector is 100x20 m, aligned North-South
	- cloverleaf feed design, spread across 80 m along the focal line and separated by 30.48 cm
	- focal length is 5m => introduces a 30 MHz ripple (this is the metal bar thing) from reflections 
	- Each feed has 2 orthogonal linear polarizations => :256 × 4 × 2 = 2048 signal inputs

* **Analog signal chain:**
	* Feed => LNA => 400–800 MHz bandpass filter =>  transmitted to two receiver huts, positioned between the cylinders, via coaxial cables.

* **Digitizers:** 
	* Once at the receiver huts
	* done by the ICE boards - 128 specialized boards which contains Analog-to Digital Converters (ADCs) and Field Programmable Gate Arrays (FPGAs)
	*  Uses 8-bit precision the ICE ADC runs at 800 million samples per second ($\nu_s = 800$ MHz)
	*  Information is recorded from the 2nd Nyquist zone i.e $ν_s/2$ to $\nu_s$ and the channel at the Nyquist frequency is discarded **Question: WHAT DOES THIS MEAN?**

 * **FX engine:** 
	 * After signal is digital 
	 * **F Engine**
		 * F is for Fourier - which makes freq channels, X is for correlater which component cross-correlates the signal with the sky positions to beamform or generate visibilities
		 * F-engine consists of custom-designed FPGAs that channelize the data into 1024 channels using a polyphase filter bank (PFB) 
		 * The PFB, in simplified terms, is a complex Fast Fourier Transform (FFT) routine that employs a window function to minimize spectral leakage 
			 * This process extracts the phase information from the signal, (the complex part is the phase, the real is the amplitude)
			 * Then there is a gain, and phase offset to the data per channel 
			 * rounded to a complex number with 4 bits (real) + 4 bits (imaginary), reducing the data rate to 6.5 Tb/s.
		* The output from the F-engine consists is 1024 channelized voltages, each with a frequency resolution of 390 kHz and a time resolution of 2.56 µs.
		* a re-arrangement step, in such a way that 2048 inputs from 4 frequency channels are received by each X-engine 
	* **X Engine** 
		* ? It is **L0**

* **Calibration:**
	* Beam forming involves pointing the telescope (Making a beam!) at a given direction by adjusting the geometric light travel time 
	* the signal chain introduces additional stochastic and instrumental delays that should be calibrated for 
	* The complex gain $G$ :
		* Behold the complex gain $G = Ae^{-i \phi }$ , A is the amplitude, and $\phi$ is the phase
		* Determined sidereal day
		* well-characterized persistent sources, such as Cas A, Cyg A, Vir A, or Tau A
		* Depends on time of year, and visibility of source for which is used
		* computed gains are applied to calibrate the signals from each antenna, across all frequency channels and both polarization modes. The gains are also stored offline and made accessible for baseband analysis 
	* Accurate calibration is critical for effective beamforming
	* n cases where calibration data cannot be recorded, previously computed gains from earlier dates are used
	* **Question: Before the L0?**

## L0 stage - Beamforming and Upchannelization
- **L0 is the X-engine!** 
- is responsible for beamforming the data and upchannelizing it to aid in FRB searches
- the zeroth stage of the transit pipeline. 
- **Question: Why are they called L's?**
- **Beamforming**: 
	- The X-engine consists of 256 GPU-equipped nodes, with each node receiving 2048 inputs corresponding to 4 frequency channels (256 × 4 = 1024) (with 2 polarizations)
	-  separated by 100 MHz in frequency to ensure that, in the event of a node failure, there are no significant gaps in the dataset.
	- The primary task of the X-engine is to perform a spatial-to-angular transform **Question: what is this?**
	- CHIME/FRB employs an FFT-based beamforming algorithm
	- The system generates 512 beams in the north-south direction, and these beams are then clamped to the nearest beam center such that beams at different frequencies share the same central beam position. The beam spacing in the north-south direction is evenly distributed in sin θ, where θ denotes the angle from zenith. 
		- This method naturally accounts for the curvature of the sky. 
	-  The four columns in the east-west direction are formed using exact phasing **QUESTION: what does this mean?**
	- For CHIME, we selected a configuration of 1024 beams extending in zenith angle from −60◦ to +60◦ from North to South.
	- FWHM of the beams is approximately 0.25◦ at 800 MHz and 0.5◦ at 400 MHz.
	- Incoherent beam also exists (has lower sensitivity by factor $\sqrt(N)$) but usefull for RFI monitoring) 
- CHIME/Pulsar uses 10 beams w/ a time resolution of 2.56 µs. 
- **TODO Copy Ketan Fig 2.2**
- 
- 




**I am on: 2.1.4.2 Upchannelization**




