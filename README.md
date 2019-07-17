# DIMEpy: Direct Infusion MEtablomics processing in python

[![Project Status: Active - The project has reached a stable, usable state and is being actively developed.](http://www.repostatus.org/badges/0.1.0/active.svg)](http://www.repostatus.org/#active)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/DIMEpy.svg)

Python package for the high-thoroughput nontargeted metabolite fingerprinting of nominal mass direct injection mass spectrometry directly from mzML files.

This is very much an implementation and extension of the  of the methods detailed in [High-throughput, nontargeted metabolite fingerprinting using nominal mass flow injection electrospray mass spectrometry (Beckmann, et al, 2008)](https://www.nature.com/articles/nprot.2007.500).

## Features

- Loading mass spectrometry files from mzML.
  - Support for polarity switching.
  - MAD-estimated infusion profiling.
- Spurious peak elimination.
- Spectrum export for direct dissemination using Metaboanalyst.
- Spectral binning.
- Value imputation.
- Spectral normalisation.
  - including TIC, median, mean...
- Spectral transformation.
  - including log10, cube, nlog, log2, glog, sqrt, ihs...
- Export to array for statistical analysis in Metaboanalyst.

## Installation

DIMEpy requires Python 3+ and is unfortunately not compatible with Python 2. If you are still using Python 2, a clever workaround is to install Python 3 and use that instead.

You can install it through ```pypi``` using ```pip```:

```
pip install dimepy
```

If you want the 'bleeding edge' version this, you can also install directly from this repository using ```git``` - but beware of dragons:

```
pip install git+https://www.github.com/AberystwythSystemsBiology/DIMEpy
```

## Usage

To use the package, type the following into your Python console:

```python
>>> import dimepy
```

At the moment, this pipeline only supports mzML files. You can easily convert proprietary formats to mzML using [ProteoWizard](http://www.proteowizard.org/download.html).

## Loading a single file

If you are only going to load in a single file for fingerprint matrix estimation, then just create a new spectrum object. If the sample belongs to a characteristic, it is recommend that you also pass it through when instantiating a new ```Spectrum``` object.

```python
>>> filepath = "/file/to/file.mzml"
>>> spec = dimepy.Spectrum(filepath, identifier="example", stratification="class_one")
/home/keo7/Projects/RachelSputum19/tissue-MZML/t1-1.mzML
```

By default the Spectrum object doesn't set a snr estimator. It is **strongly recommended** that you set a signal to noise estimation method when instantiating the Spectrum object.

If your experimental protocol makes use of mixed-polarity scanning, then please ensure that you limit the scan ranges to best match what polarity you're interested in analysing:

```python
>>> spec.limit_polarity("negative")
```


If you are using FIE-MS, then it is strongly recommended that you use just the infusion profile to generate your mass spectrum. For example, if your scan profiles look like this:

```
        |        _
      T |       / \
      I |      /   \_
      C |_____/       \_________________
        0     0.5     1     1.5     2 [min]
```

Then it is fair to assume that the infusion occured during the scans ranging from 30 seconds to 1 minute. The ```limit_infusion()``` method does this by estimating the mean absolute deviation (MAD) of total ion counts (TIC) before limiting the profile to the range between the time range in which whatever multiple of MAD has been estimated:

```python
>>> spec.limit_infusion(2) # 2 times the MAD.
```

Now, we are free to load in the scans to generate a base mass_spectrum:

```python
>>> spec.load_scans()
```

You should now be able to access the generated mass spectrum using the ```masses``` and ```intensities``` attributes:

```python
>>> spec.masses
array([ ... ])
>>> spec.intensities
array([ ... ])
```



## Bug reporting and feature suggestions

Please report all bugs or feature suggestions to the [issues tracker](https://github.com/AberystwythSystemsBiology/DIMEpy/issues). **Please do not email me directly** as I'm struggling to keep track of what needs to be fixed. 

We welcome all sorts of contribution, so please be as candid as you want(!)

## Contributors

* **Lead Developer:** Keiron O'Shea (keo7@aber.ac.uk)
* **Project Supervisor:** Chuan Lu (cul@aber.ac.uk)
* **Project Supervisor:** Luis AJ Mur (lum@aber.ac.uk)
* **Methods Expert:** Manfred Beckmann (meb@aber.ac.uk)

## License

DIMEpy is licensed under the [GNU General Public License v2.0](https://raw.githubusercontent.com/AberystwythSystemsBiology/DIMEpy/master/LICENSE).
