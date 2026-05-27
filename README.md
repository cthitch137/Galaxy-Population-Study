# Multi-Perspective Study of Galaxy Populations

Active galactic nuclei (AGN) activity is known to affect galaxy morphology, and both stellar mass and metallicity from SED fits feed directly into habitability models. This is a research project consisting of three primary sections. The natural link between them all is a shared galaxy sample, interrogating the same galaxies through different lenses.

## Project Overview
| Project | Lens | Methods |
|---------|---------|---------|
| P1 | AGN & host galaxy properties | SED fitting (BAGPIPES), AGN identification via BPT diagrams or X-ray/IR flags |
| P2 | Morphology & structural properties | statmorph / AutoProf on imaging, Sersic fitting, visual classification |
| P3 | Galactic habitability context | Stellar mass, SFR, metallicity gradients -> habitability proxies from literature models |
```
galaxy-survey/
│
├── README.md                              # This file overviewing all three projects
├── data/
│   └── sample_catalog.csv                 # Galaxy Sample
│
├── 00_sample_selection.ipynb              # Shared pipeline - run first
│
├── 01_agn_host_galaxies/
│   ├── agn_notebook.ipynb
│   └── README.md
│
├── 02_morphology_structure/
│   ├── morph_notebook.ipynb
│   └── README.md
│
├── 03_habitability_context/
│   ├── hab_notebook.ipynb
│   └── README.md
│
└── utils.py                               # Shared helper functions
```

## Getting started with sample selection

### Step 1 — Query SDSS DR17 
The ```00_sample_selection``` notebook is the foundation everything builds from. Using ```astroquery```, we can use filters like:
* Object type = galaxy
* Redshift range (e.g. 0.02 < z < 0.25 — nearby enough to be resolved, far enough to be interesting)
* Signal-to-noise cuts on spectra
* Has imaging in *ugriz*

When querying, its important to consider the redshift range. At low-z, SDSS imaging is well resolved enough to do reliable morphology. Above $z \sim 0.15$ or so, galaxies start becoming compact enough that morphological measurements get noisy. So by using a flat sample across 0.02-0.3, the morphology portion of the project would be fighting the upper half.

Rather than two separate samples, we will instead use one master query with a built in split:
| **Tier** | **Redshift** | **Size** | **Primary use** |
|-------------|-------------|-------------|-------------|
| **Full sample** | $0.02 < z < 0.25$ | $\sim 2000$ galaxies | P1 (AGN), P3 (habitability) |
| **Low-z subset** | $0.02 < z < 0.1$ | $\sim 500-800$ of the above | P2 (morphology) |

The low-z subset is just a filter on the same catalog, therefore no extra querying. This way all three projects are still drawing from the same pool of galaxies, keeping the shared data link intact, but P2 is working with the cleanest image.

### Step 2 — Exporting
All that is left is to export a clean ```sample_catalog.csv``` that all three notebooks read from.

# Dependencies
During this project, the following packages were used (this is subject to change):
* numpy (2.02)
* matplotlib (3.10.0)
* astropy (7.2.0)
* astroquery (0.4.11)
* pandas (2.2.2)