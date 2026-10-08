# Preparation before the workshop
This is the list of useful tools we plan to use for our team hack sprint. <br>
The ways to install each package/pipeline are listed on the link to 'How to Install'. <br> 
The client or package with
- 🚨: essential to install
- ⭐: recommended to set up
- 💡: optional, but useful. 

## Rubin Alert Brokers 
### ALeRCE 🚨 
- Summary: a Chilean-led Community Broker for the Vera C. Rubin Observatory and its Legacy Survey of Space and Time (LSST).
- Website: https://science.alerce.online/
- How to set up client: https://alerce.readthedocs.io/en/latest/
- Tutorials: https://github.com/alercebroker/usecases/tree/master/notebooks/LSST
  
### Lasair 🚨 
- Summary: a UK Alert Stream Broker to serve transient alerts from the Rubin Legacy Survey of Space and Time (LSST) to the astronomical community. 
- Information: https://lasair.lsst.ac.uk/
- How to set up client: https://lasair-lsst.readthedocs.io/en/main/core_functions/client.html
- Tutorials: https://lasair-lsst.readthedocs.io/en/main/core_functions/python-notebooks.html
  
### Fink 💡
- Summary: an astronomical alert broker that serves as an intermediary between alert issuers and the scientific community analyzing the alert data.
- Description: Among its various functions, Fink collects and stores alert data, enriches it with information from other surveys and catalogs, as well as user-defined enhancements like machine-learning classification scores. It also redistributes the most promising events for further analysis, including follow-up observations.
- Information: https://fink-broker.org/
- How to set up client: https://doc.ztf.fink-broker.org/services/fink_client/
- Tutorials: https://github.com/astrolabsoftware/fink-tutorials

## Rubin Data & LINCC Tools
### LightCurveLynx ⭐
- Summary: A fast and nimble package for realistic time-domain light curve simulations
- Website: https://lightcurvelynx.readthedocs.io/en/latest/#
- How to set up: https://lightcurvelynx.readthedocs.io/en/latest/index.html#installation
- Tutorials: https://lightcurvelynx.readthedocs.io/en/latest/notebooks/introduction.html
  
  
### LSDB 🚨: 
- Summary: A Python tool for scalable analysis of large catalogs (e.g., analyzing, querying, and/or crossmatching $\sim10^9$ sources)
- Information: https://docs.lsdb.io/en/latest/tutorials/rubin_dp2_release.html
- How to Install: https://docs.lsdb.io/en/latest/getting-started.html
- Tutorials: https://docs.lsdb.io/en/latest/tutorials.html
  

## Host Galaxy Association & Characterization
Let's try to use at least one of the packages listed below) 

### The Deep Learning Identification of Galaxy Hosts in Transients (DeLight) 🚨
- Summary: A library created by the ALeRCE broker to automatically identify host galaxies of transient candidates using multi-resolution images and a convolutional neural network
- Information: Förster et al. 2022 ([ads](https://ui.adsabs.harvard.edu/abs/2022AJ....164..195F/abstract))
- How to Install: https://pypi.org/project/astro-delight/
- Tutorials: [Jupyter notebook](https://nbviewer.org/github/fforster/DELIGHT/blob/main/notebook/Delight_example_notebook.ipynb)  or https://colab.research.google.com/github/fforster/DELIGHT/blob/main/notebook/Delight_example_notebook.ipynb
- 
### Pröst 🚨
- Summary: A code for host-galaxy identification of extragalactic transients. It’s fast, probabilistic, and highly customizable.
- Information: https://astro-prost.readthedocs.io/en/latest/
- How to Install: https://astro-prost.readthedocs.io/en/latest/
- Tutorials: https://astro-prost.readthedocs.io/en/latest/notebooks/associate.html
  
### Blast (optional) ⭐
- Summary: The host galaxies of astrophysical transients play a key role in our understanding of their progenitor systems and the use of Type Ia supernovae as standardizable candles for cosmology. 
- Information: [Homepage] (https://blast.scimma.org/)
- How to run it locally: https://blast.readthedocs.io/en/latest/developer_guide/dev_running_blast.html
 
### Frankenblast (optional) ⭐ 
- Summary:
- Information: 
- How to Install:
- Tutorials: 

## Others

### DataLab ⭐
- Summary: 
- Homepage: https://datalab.noirlab.edu/?r=0
