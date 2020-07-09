---
layout: post
title: Fetching Sentinel 2 Data from Sentinel HUB using eo-learn
excerpt: Application of python in RS
created: 07/10/2020, 00:00:24
tags: [RS, Sentinel2, SentinelHUB, Python script]
comments: false
category: blog
---

The open Earth observation (EO) data are accesible via the Copernicus and Landsat programs which large resource for many EO applications, including ocean and land use and land cover monitoring, disaster control, emergency services and humanitarian relief. Given the large amount of high spatial resolution data at high revisit frequency, techniques able to automatically extract complex patterns in such spatio-temporal data are required.
{: style="text-align: justify"}

[eo-learn](https://eo-learn.readthedocs.io/en/latest/) is a collection of open source Python packages that have been developed to seamlessly access and process spatio-temporal image sequences acquired by any satellite fleet in a timely and automatic manner. eo-learn is easy to use, it’s design modular, and encourages collaboration – sharing and reusing of specific tasks in a typical EO-value-extraction workflows, such as cloud masking, image co-registration, feature extraction, classification, etc. (info. adopted eo-learn Docs)
{: style="text-align: justify"}

[eo-learn](https://eo-learn.readthedocs.io/en/latest/) library acts as a bridge between Earth observation/Remote sensing field and Python ecosystem for data science and machine learning. The library is written in Python and uses NumPy arrays to store and handle remote sensing data. Its aim is to make entry easier for non-experts to the field of remote sensing on one hand and bring the state-of-the-art tools for computer vision, machine learning, and deep learning existing in Python ecosystem to remote sensing experts.(info. adopted eo-learn Docs)
{: style="text-align: justify"}

In this post, I would like to use retrieve the time series Sentinel-2 Level-2A for the area in the south of Finland. For this purpose, you need to have an [Sentinel Hub](https://services.sentinel-hub.com/oauth/subscription) account. It is required to create a new configuration ("Add new configuration") and set the configuration to be based on **Python scripts template**. After you have prepared a configuration please put configuration’s **instance ID** into sentinelhub package’s configuration file following the [configuration instructions](https://sentinelhub-py.readthedocs.io/en/latest/configure.html). For Processing API request you also need to obtain and set your oauth client id and secret.


# Set up the configuration 

{% highlight python %}
%reload_ext autoreload
%autoreload 2
%matplotlib inline
!sentinelhub.config --instance_id ''
!sentinelhub.config --sh_client_id ''
!sentinelhub.config --sh_client_secret ''
!sentinelhub.config --show 
{% endhighlight %}

# Import the required libraries

{% highlight python %}
# Add generic packages
import os
from matplotlib import dates
from tqdm.notebook import tqdm as tqdm
from pathlib import Path
from mpl_toolkits.axes_grid1 import make_axes_locatable

from shapely.geometry import Polygon, box, shape, mapping
import matplotlib.pyplot as plt
import geopandas as gpd
import pandas as pd
import numpy as np
import overpass

# eo-learn and sentinelhub imports
from eolearn.core import EOTask, EOPatch, LinearWorkflow, FeatureType, OverwritePermission, \
    LoadFromDisk, SaveToDisk, EOExecutor, SaveTask
from eolearn.io import SentinelHubInputTask, SentinelHubDemTask, SentinelHubInputBase, get_available_timestamps,ExportToTiff
from eolearn.mask import AddCloudMaskTask, get_s2_pixel_cloud_detector, AddValidDataMaskTask
from eolearn.geometry import VectorToRaster, PointSamplingTask, ErosionTask
from eolearn.features import LinearInterpolation, SimpleFilterTask
from sentinelhub import BBox, CRS, DataSource, MimeType, SentinelHubRequest, SHConfig,BBoxSplitter
{% endhighlight %}