# DETECT

Developed by Tobias Géron at the University of Toronto. Created Summer 2024. Last updated July 2025. This code was last tested using the r29.1.1 image on the RSP. This package is now compatible with DP1. Please see below for a full list of package compatibility.

DETECT (Detection Efficiency and Threshold Estimation for Characterization of Transients) was developed to help better quantify detection thresholds with the Rubin pipelines. The current Rubin pipelines search for detections with a simple SNR threshold in difference images. However, this becomes difficult when there is a bright background galaxy, or when the datapoint in question has a magnitude around the detection threshold (~23-24). How confident are we that this detection is real?

This code was created with a specific science goal in mind: pre-SN variability. However, it can be used in any science context where you want to better understand the detection thresholds. We aim to answer the question: "Using the Rubin pipelines, in this specific epoch, using this specific template, how likely is it that a datapoint of X mag is detected? And what are the detection thresholds in this specific situation?" 

This is answered using a series of source injection, image subtraction, and forced photometry. The output is a recovery curve, which you can use to compute the 80% or 50% detection fraction thresholds (i.e. the magnitude where X% of sources with magnitude Y would be detected with the Rubin pipelines at this specific epoch and this specific template).

Finally, I am happy to help anyone with using this package and welcome feedback or suggestions for features. Please feel free to contact me at tobias.geron@utoronto.ca. I would also appreciate it if you let me know when you use this code in a publication.



### Minimal example

Below you can find a minimal example of how to use this package. However, it is strongly encouraged to have a look at the more in-depth tutorials as well. 

First, import the package.

```
from detect import *
```

Then, run the main part of the code. This will take some time, depending on the settings. See the in-depth tutorial for more detail on how to speed this up, if needed. You need a magnitude, the science and template exposures, other sources in the image, the SN coordinates, and the config object.

```
config = recovery_curve_config()
df_recovery = recovery_curve(sn_mag = mag, science_exposure = calexp, template_exposure = template, sources = sources, sn_position = (ra,dec), config = config)
```

You can then use that output to obtain the X% detection fraction thresholds.

```
thresholds = find_thresholds(df_recovery, detection_fraction_thresholds = [0.5,0.8])
```

Which you can compare to the magnitude of your detection. It is also strongly advised to correct your detection magnitude for any background signal when making the comparison:

```
df_background = estimate_sn_background(calexp, template, sources = sources, sn_position = (ra,dec))
sn_mag_corrected = njy_to_mag(mag_to_njy(sn_mag) - np.median(df_background['base_PsfFlux_nJy'])) # Convert SN magnitudes to nJy, then subtract median of the background, then convert back to mag. 
```

Finally, you can then compare `sn_mag_corrected` to the previously established thresholds (in `thresholds`) to determine whether the detection is real. 



### Need more details?

Please refer to the example notebooks for more detail (highly recommended!). We also recommend to run `help(recovery_curve)` and `help(recovery_curve_config)` for more information on the additional settings and parameters that you can specify.




### How to run this code

You have two options: either run this locally on your device, but then you would need to have the Rubin pipelines installed as well (see https://pipelines.lsst.io/), which might be difficult. It is probably easier to copy this folder to the Rubin Science Platform (https://data.lsst.cloud/), load the appropriate image, and run it on the RSP. It is highly recommended to load a large batch (4 CPU and 16 GB RAM). 



### Package compatibility and versions
You need access to the Rubin pipelines for this to work. Ideally, you would run it directly on the Rubin Science Platform. 

This code was last tested using the r29.1.1 image on the RSP. It is highly recommended to load a large batch (4 CPU and 16 GB RAM). 

In addition, you'll also need:  
numpy 2.2.6  
matplotlib 3.10.3  
tqdm 4.67.1  
astropy 7.1.0  
pandas 2.3.0  
scipy 1.13.1  
pyyaml 6.0.2

(Though these are all included in the r29.1.1 image in the correct version, so if you're running this code on the RSP using the correct image, don't worry about it.)
