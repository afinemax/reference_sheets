

**Last Updated:** July 2025
**Current Maintainer**: Maxwell A. Fine email: maxwell.fine@mail.mcgill.ca 

---
# Overview

Exposure refers to the observation duration of a given sky region above a defined system sensitivity threshold, and is essential for estimating the all-sky FRB rate, studying repeaters, and planning multi-wavelength follow-up. Accurate exposure estimates enable robust population modeling and help track variability across the electromagnetic spectrum.

To first order, CHIME/FRB operates 24/7. Assuming constant sensitivity, the exposure is calculated as the transit time of sky positions across the FWHM of the CHIME beam(s) at **600 MHz**. Currently it is not possible to calculate exposure for a different reference frequency, 600 MHz is used as the beams do not overlap. 

However, CHIME/FRB in reality does not operate truly 24/7 — there are sometimes issues with parts of the CHIME/FRB detection pipeline (L0–L4), which are monitored by Grafana **metrics** and the FRB-Tsars. There can also be maintenance at the site, power failures, node failures, thermal shutdowns, etc. 

The sensitivity also changes due to outside temperature and other environmental effects, and this variability is tracked using **pulsar metric files**. The general idea is that for a population of pulsars, their average signal should remain roughly constant. CHIME/FRB can identify days of poor sensitivity by comparing the observed S/N values against a historical average derived from a reference catalog of pulsars. Days with 10% higher then the median relative RMS noise are excised.  The reference time is -. 


**The CHIME/FRB exposure pipeline:**
1. Keeps track of when CHIME/FRB is not operating normally (L0–L4) using **metric files** to determine the usable transit time.
2. Uses a calibration catalog of known pulsars to estimate the relative sensitivity of CHIME/FRB. This occurs once per sidereal day when the Galactic plane transits (as most pulsars are in the Galactic plane). These are referred to as **pulsar metric files**. Bad days are removed from the exposure calculation. 
3. Uses an additional metric file - the `callback files`, to account for times when the system is operating poorly but this is not well captured by the aforementioned metrics.

**Outputs of the pipeline are**:
1. The various metric files for L0 - L4, the CHIME/FRB sensitivity information.
2.  Pixelated exposure maps from a given `start-date` to `end-date`. 
3. The number of beam days between a given date range and also exposure in each beam with a plot.
4. The exposure of a single sky position. 

For more information, see the CHIME/FRB exposure pipeline GitHub repository [here](https://github.com/CHIMEFRB/frb-exposure/tree/master/exposure). Chapters 2 and 3 of Pragya’s thesis: [link](https://escholarship.mcgill.ca/concern/theses/db78tj079) Chapter 4 of Ketan’s thesis. 

---

# The Metric Files:

The metric files are a core component of the CHIME exposure pipeline, providing key data used to assess and quantify exposure across different pipeline levels. Metrics for Level 1 (L1) are generated from dead beam information, while Levels 2 through 4 (L2–L4) rely on metrics obtained from Grafana, which interfaces with Prometheus. Pulsar-related metrics are sourced from the known source database within L4. All metric files—including those for L0–L4, pulsars, and callbacks—are organized into dedicated directories and stored on the `frb-analysis` server at: `frb-analysis:/data/chime/intensity/processed/exposure`.

```
ls user@frb-analysis:/data/chime/intensity/processed/exposure

L1_logs/
L2L3_logs/
L4_logs/
pulsar_data/
callback_logs/
```

> 💡 **Hint:**  
> When working in the metric file directories, it's recommended to use:
> `ls -ltrh` 
> This will list the newest files first in a human-readable format.

### L1_logs:
- Contains `.npz` files following a naming convention of `beam_exposure2025-06-16-2025-06-17_N.npz`, there is one per day.  The `.npz` file contain `t_stamp`, `exposure_2D`, `beam_names` and they form a binary 2D mask of on and off times.
- Only record when the beam turns on/off. 0 is off, 1 is on.  Time resolution of 4 seconds.  

### L2L3_logs:
- Contains `.txt` files following a naming convention of `L2L3_off_times_16-06-2025.txt`, there is one per day.  
- These contain the **start**, **stop** times of when L2L3 was **OFF**
- The L2L3  logs record at a time resolution of 4 seconds.

```
# Example L2L3 log file
2025-06-12 00:00:00+00:00 # start of L2L3 Being off
2025-06-12 03:55:00+00:00 # end of L2L3 Being off
# another entry 
# start time
# end time
```

### L4_logs:
- Contain `.txt` files following a naming convention of `L4_off_times_16-06-2025.txt`, there is one per day. 
- These contain the **start**, **stop** times of when L4 was **OFF**
- The L4 logs record at a time resolution of 4 seconds. 

```
# Example L4 log file
2025-06-16 03:49:00+00:00 # start of L4 Being off
2025-06-16 03:55:00+00:00 # start of L4 Being off
# another entry 
# start time
# end time
```

### pulsar_data:

> ⚠️ **Important Note:**  
> If the most recent files are **not** the following, named with a `yymmdd`  then the most recent **pulsar metric generation run has failed**.  These are generated at the end of a pulsar metric run. 
> 
> You should:
> - Notify the current maintainer of the exposure pipeline.
> - Check the relevant logs for more details.
>
> Example files:
> - `list_gain_files_yymmdd.txt`
> - `low_sensitivity.txt`
> - `low_sensitivity_yymmdd.txt`
> - `CHIME_FRB_sens_excised_10_percent.png`
> - `RMS_variation_all_pulsars.npz`
> - `RMS_variation_all_pulsars_yymmdd.npz`
> - `rms_variation_hist.png`
> 
> **Additional Caution:**  
> The `conda` environment on `frb-analysis` uses an **outdated version of `pandas`** compared to the Docker-based exposure pipeline.  As a result, you may not be able to open some of the newer metric output files (e.g., `RMS_variation_all_pulsars_250421.npz`) using the `frb-analysis` version of `pandas`.  **Workaround:** Copy the files to your local machine and open them using an up-to-date environment.


#### Multiple file types:
 - the ending of `yymmdd` is made for the end date of the last metric run

- `ksqa` files which are `.npy` files following `ksqa_results_all_var_J2346-0609_180828-250312.npy` - ksqa is known source quality assurance 
	- two per pulsar, one with a date range of `180828-250312` 
	- the new ones with `250313-yymmdd` 
	- Contain various observation data about the pulsar which is used for the sensitivity pipeline: `transit_time`, `no_events`, `max_snr`, `min_snr`, `mean_snr`, `median_snr`, `std_snr`, `q_factor_mean`, `q_factor_std`, `q_factor_med`
- `list_gain_files_yymmdd.txt`
	-  The relevant gain files used for the pulsar calculation per day

```
# `list_gain_files_yymmdd.txt`
total 56725022
-rw-r--r--. 1 2060  2060 16861232 Aug 31  2018 gain_20180830T074323.149037Z_cyga.h5
-rw-r--r--. 1 2060  2060 16863328 Sep  2  2018 gain_20180902T061930.796038Z_cyga.h5
-rw-r--r--. 1 2060  2060 16863328 Sep  4  2018 gain_20180904T061515.623403Z_cyga.h5
-rw-r--r--. 1 2060  2060 16862768 Sep  5  2018 gain_20180905T061227.338037Z_cyga.h5
```

* `low_sensitivity.txt` and `low_sensitivity_yymmdd.txt`
	* Basically the same file, containing the `start` and `stop`  times of when CHIME/FRB had low sensitivity - which will be excised in the exposure calculations.  From the start of recording in 2018.
	* Each bad day is ~1 sidereal day
	* `yymmdd` stops on the the yymmdd of the last generate metric day, the other `.txt` may go further if `yymmdd` is not the most recent. 

```
#low_sensitivity.txt
2018-08-30 07:43:23.149037+00:00 # start low sensitvity 
2018-08-31 07:39:27.239567+00:00 # end low sensitiviy 
2018-08-31 07:39:27.239567+00:00 # start low sensitivity 
2018-09-01 07:35:31.330097+00:00 # end low sensitivity 

```

* `CHIME_FRB_sens_excised_10_percent.png` - a plot of the CHIME sensitivity vs time showing the excised times

![[CHIME_FRB_sens_excised_10_percent 1.png]]

* `rms_variation_hist.png` - a plot showing the relative RMS noise 
![[rms_variation_hist.png]] 
* `RMS_variation_all_pulsars.npz`, and `RMS_variation_all_pulsars_250312.npz`
	* contain `cal_times`, `sens_dec`, `std_dec`, `to_excise`, `num_pulsars`, `dtype`
	* Used in the sensitivity calculation. 

### callback_logs
- `.txt` files following `CB_off_times_18-07-2025.txt`
- Hand made, and only made when needed - so there is not necessarily a file every day
- Contain the `start` and `stop` times of when CHIME or CHIME/FRB was not operating normally, but not captured by the other metrics.
- The current exposure pipeline maintainer makes these by looking at the `frb-ops` slack channel for bad system times

```
# `CB_off_times_dd-mm-yyyy.txt`
2018-08-31T13:29:03.696357000 # start poor operations
2018-08-31T18:28:12.654094000 # end poor operations
```



---
## Generating the Metrics
> ⚠️ **Important Note:**  
> Users of the exposure pipeline should not be running the metric generation script, ping the current exposure pipeline maintainer and they will run it it is out of date. 

To make the metrics follow these steps. The metrics are used to 1) know when CHIME is (not) operating for exposure calculations, and 2) calibrate CHIME's sensitivity using Pulsars

1. ssh into l4 / precommissioning ([Directions on CHIME wiki](https://bao.chimenet.ca/wiki/index.php/FRB_Operations_Manual))
	1. generating the metrics requires access to the L4 database
2. `cd  frb-exposure/metrics`
3. or exposure/metrics
* Now runs in docker, check the docker `yml` but the command should look similar to this: (modify for the number of days)
	* `python /src/exposure/metrics/generate_metrics_old.py --l1_days 5 --l2l3l4_days 5 --psr_metric 5
	* see the program outline for [[generate_metrics_old.py.md]] for an overview of the program
1. The metrics are written to frb-analysis:
2. ssh into `frb-analysis` (`user@frb-analysis` from the CHIME network)
3. check `/data/chime/intensity/processed/exposure`
	* l1, l2l3, l4, pulsar, callbacks
	* either some `.npy` or `.npz` file or a `.txt` file containing the window (start_off, end_off) of time CHIME was off 
	* **callbacks are handwritten**  and apply for bad system times not captured by the metrics 
	* pulsar's have more information known source quality insurance (ksqa), and excised times
* If generating the metrics, and the ending pulsar files are not generated then it error-ed out (check L4, and docker logs)

---
## Using the exposure pipeline aka Using the metrics:

The main notebook to use the metrics is located [here - submit_workflow_jobs.ipynb][https://github.com/CHIMEFRB/frb-exposure/blob/master/exposure/submit_workflow_jobs.ipynb]. This notebook well documented. Under the hood the notebook calls programs to run on `frbadmin@vsp`, so you can start it on your local computer! Be sure to have CHIME `workflow`Make sure you have the workflow installed locally see [here] [https://github.com/CHIMEFRB/workflow]. You are able to launch multiple instances of a running task IE calculate exposure maps for different date ranges, or exposure for a position. 
## submit_workflow_jobs.ipynb is used for  generating:
- **Pixelated exposure maps** of the entire sky for a start and end date 
	- More info on the function is [here] [https://github.com/CHIMEFRB/frb-exposure/blob/master/exposure/generate_daily_exp_maps_workflow.py]
- **Combine pixelated exposure maps**
- **Query exposure maps**
	- This the script to get exposure for multiple event ids. Useful for catalog level papers.
- **Generate Beam days**
	- It gets how many beams were active between a given date range and also exposure in each beam with a plot. More info on this can be found [here][ https://github.com/CHIMEFRB/frb-exposure/blob/master/exposure/exposure_beam_days_workflow.py]
- **Get exposure for one sky position**
	- it can get the exposure for just one position in the sky, given the RA and DEC (in degrees). If you don't have exposure maps generated.
	* More info on the code can be found [here] [https://github.com/CHIMEFRB/frb-exposure/blob/master/exposure/exposure_detailed_per_src_workflow.py]
### Calculate_transit_and_FWHM_extent.ipynb
- This is another notebook for calculating the transit, and FWHM extent for a single `event_id` given its RA, and DEC

---
## Maintenance:

> ⚠️ **Important Note:**  
> Do not delete anything in the exposure directory! Ping the current maintainer if you feel tempted to use `rm`, or pulsar metrics did not complete. 

### Docker Workflow
- Use a dummy command with a long sleep time when running Docker containers, then enter them in interactive mode.
- Make small code modifications inside the container, test them, and then:
- Push changes to GitHub.
- Open a merge request.
- After two merge approvals, CI/CD will automatically trigger. This is a Github action.

### Package Management
- Check the Docker image version against the version on GitHub.
- When installing new packages:
- Use `pip` to determine the version number.
- Add the package with `poetry` using the correct version.
- Use `commitizen` for formatting commit messages and GitHub pushes.

### Programs on `frbadmin@vsop`
- Apply the same Docker modifications to the relevant YAML files.
- Look for the `docker-compose` YAML that handles exposure and launching of the workflow.
- Check the Docker image version used in these files.
- You’ll need the `frbadmin` password to access and modify these resources.
