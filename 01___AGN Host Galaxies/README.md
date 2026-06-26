# AGN Activity and Host Galaxy Properties

Of the 229 emission line galaxies identified, 46 were classified as AGN hosts, 120 as star-forming, and 63 as composite. Spectral Energy Distribution (SED) model fitting was performed on the 46 AGN hosts using BAGPIPES.

## BPT Classification
To analyze the sample galaxies, we first need emission line data which was grabbed from SDSS and then matched with the same catalog from sample selection. The emission line data consisted of [OIII], Hβ, [NII], and Hα. With these lines you can plot the ratio of [OIII] / Hβ against [NII] / Hα. From this we can see what ionizes the gas in the galaxy by using the Kauffmann+03 and Kewley+01 demarcation lines, classifying the galaxies into star-forming, composite, and AGN dominated populations. In order to tell whether or not it's an AGN, we look for high energy ionization as these ratios are distinct from those of young hot stars seen in star-forming regions. 

## BAGPIPES
The Bayesian Analysis of Galaxies for Physical Inference and Parameter Estimation, BAGPIPES, is a python tool used for fitting SED models generally designed for stellar light. Its primary function is estimating galaxy stellar mass and Star Formation Rate (SFR). This makes analyzing AGNs tricky since passing AGN dominated photometry into the BAGPIPES pipeline without adjustments to the fit instructions can result in improper SED fitting as the blue continuum can get misinterpreted as a massive burst in star formation.

There are four key steps to BAGPIPES: 
* Loading filter curves that tell BAGPIPES which wavelengths were observed
* `load_data` function that feeds flux measurements into BAGPIPES
* `fit_instructions` defines the model (SFH, dust, redshift)
* Running the fit with `bagpipes.fit(galaxy, fit_instructions)`

## `fit_instructions`
This is a dictionary made up of tuples of which are uniform priors. It tells BAGPIPES the kind of physical model we want it to compare to the observed data. The specific parameters are as follows:

| Category | Parameter | Range | Definition |
| :---: | :--- | :---: | :--- |
| **Star Formation** | `tau` | (0.1, 15) | e-folding timescale in Gyr |
|  | `massformed` | (1, 13) | log10 stellar mass formed |
|  | `age` | (0.1, 13) | Age of the galaxy in Gyr |
|||||
| **Dust Attenuation** | `type` | Calzetti | Model curve for physical attenuation |
|  | `Av` | (0, 4) | Amount of Attenuation in the visual band |
|||||
| **Redshift** | `redshift` | `['z']` value | spec-z from SDSS (updated before each fit) |
|||||
| **AGN** | `alphalam` | -1.5 | Power law slope at shorter wavelengths (UV/blue) |
|  | `betalam` | -0.5 | Power law slope at higher wavelengths (optical/red) |
|  | `f5100A` | (1e-17, 1e-15) | Flux normalization at 5100 Angstroms |
|  | `sigma` | 1000 | Velocity broadening of AGN emission lines (km/s) |
|  | `hanorm` | 0 | Controls broad H-alpha emission the AGN contributes |

## Compiling Results
After running the fit with `bagpipes.fit()`, the results are compiled and plotted to display the fit of the SED, Star formation history, and a corner plot of the estimated parameters. These three plots are saved into the bagpipes plots folder for each galaxy. The compiled results are also saved into `.h5` files, also found in the bagpipes folder.

Additionally, for this notebook the results are placed within a `csv` to save the results of all galaxies BAGPIPES runs on.

## Comparative Analysis
### AGN hosts

![AGN and Star-forming parameter distributions](plots/parameter_comparison.png)

| Parameter | Mean 16th percentile | Mean 50th Percentile | Mean 84th percentile |
| :--- | :---: | :---: | :---: |
| Mass | 11.12 | 11.17 | 11.22 |
| Age | 6.46 | 8.29 | 9.93 |
| AV (dust) | 0.50 | 0.58 | 0.66 |
| Tau | 1.32 | 2.05 | 2.90 |

### Star-forming Galaxies

| Parameter | Mean 16th percentile | Mean 50th Percentile | Mean 84th percentile |
| :--- | :---: | :---: | :---: |
| Mass | 10.60 | 10.66 | 10.72 |
| Age | 4.80 | 6.40 | 8.16 |
| AV (dust) | 0.75 | 0.87 | 0.98 |
| Tau | 3.52 | 5.47 | 7.39 |

**Mass** - AGN hosts sit ~0.5 dex more massive than star-forming galaxies, and have a tight uncertainty (±0.05 dex). This comes out to a factor of 3 in terms of stellar mass.

**Age** - AGN hosts are significantly older, typically existing in more evolved galaxies. The older shift is clear with a similar scatter. The AGN mean age (8.29 Gyr) falls outside the one sigma range of the star-forming mean (4.80 - 8.16 Gyr), which is proven statistically significant below by the KS test.

**Dust** - Star-forming galaxies often contain high Av while AGN hosts concentrate towards lower values. Physically this means more ongoing star formation within dust-rich molecular clouds. Meanwhile AGN hosts are older and have since cleared their interstellar medium (ISM), consistent with the above values.

**Tau** - Star-forming galaxies tend to have much longer e-folding timescales, here we see 5.47 vs 2.05 Gyr. This means they've been forming stars slowly and steadily while AGN hosts declined faster.

Additionally, a Kolmogorov-Smirnov (KS) two-sample test was performed to compare whether two samples are from the same distribution. Doing so would allow us to quantify whether the AGN and Star-forming distributions are statistically distinct as opposed to visually. Here we are looking for a p-value below 0.05, meaning the distributions are statistically distinguishable. 

| Parameter | KS stat | P-value |
| :--- | :---: | :---: |
| `mass_50` | 0.370 | 0.0035 |
| `age_50` | 0.370 | 0.0035 |
| `Av_50` | 0.239 | 0.1446 |

This test tells us AGN host galaxies show statistically significant differences in stellar mass and age compared to star-forming galaxies (KS p < 0.01), consistent with AGN preferentially residing in massive systems that have had time to evolve. For Av however, the difference in dust attenuation was not statistically distinguishable (p = 0.14).

## Limitations
The AGN continuum model uses fixed power law slopes. This directly limits BAGPIPES' ability to dynamically model galaxies with non-standard AGN spectra. Photometry from GALEX/WISE would also improve the SED decomposition. By expanding the parameter space to more wavelengths, BAGPIPES can utilize more data which improves its power and accuracy.
