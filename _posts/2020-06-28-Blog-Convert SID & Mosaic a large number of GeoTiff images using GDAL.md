---
layout: post
title: Convert SID format & Mosaic a large number of GeoTiff images
excerpt: Sample bash script using GDAL
created: 06/29/2020, 00:00:24
tags: [GDAL, Mosaic, SID, Bash script, Geotiff]
comments: false
category: blog
---

Sometimes raw raster data coming with raw format which does not include any projection infomation in metadata. Using such raw data is difficult for further processing; therfore, it could be very useful to convert them to the format such as Geotiff which is usable for different purposes. On the other hand, due to large valume of the high resolution raster dataset, they are distributed in the tiles so that each tile covers only a special zone while making a mosaic out of several tiles to further analysis is compulsary.
{: style="text-align: justify"}

One of the computer software library for reading and writing raster and vector geospatial data formats, which is released under the permissive X/MIT style free software license by the Open Source Geospatial Foundation is [GDAL (Geospatial Data Abstraction Library)](https://gdal.org/). This library includes tools for both raster (gdal_translate, gdal_warp etc.) & vector (ogr2ogr, ogrmerge etc.). However, it is well-known for its tools for dealing with big raster datasets. 
{: style="text-align: justify"}

In this sample code, this powerful library was used to firstly, convert the [SID (MrSID – Multi-resolution Seamless Image Database)](https://gdal.org/drivers/raster/mrsid.html) to [GeoTiff](https://gdal.org/drivers/raster/gtiff.html) & creation of mosaic out of the converted raster tiles.Finally, create a perview of the produced mosaic with low volume.
{: style="text-align: justify"}


<img align="left" src="https://behzad89.github.io/images/tile_lt.JPG">

*Fig. 1: One tile of the orthophoto image*
{: style="text-align: center"}


# Conversion of SID to GeoTiff

in the first step, it is required to use loop in bash to read and conver each SID tile to GeoTiff one.

{% highlight bash %}
#! /bin/bash
for filename in *.sid 
do
gdal_translate -a_srs EPSG:3346 -of GTiff -co COMPRESS=DEFLATE -co ZLEVEL=9 $filename $(pwd)/tiff/${filename%.*}.tif
done 
{% endhighlight %}

# Getting the list of converted files
{% highlight bash %}
ls ./tiff/*.tif > list.txt
{% endhighlight %}
# Making mosaic out of file's list
{% highlight bash %}
gdalwarp -co BIGTIFF=YES --optfile ./tiff/list.txt ./mosaic/ort_mosaic_lithuania_2019.tif
{% endhighlight %}
# Creation of the preview from produced mosaic
{% highlight bash %}
gdal_translate -of jpeg -scale -outsize 10% 10% ./mosaic/ort_mosaic_lithuania_2019.tif ./mosaic/ort_mosaic_lithuania_2019.jpeg 
{% endhighlight %}

![mosaic img](https://behzad89.github.io/images/mosaic_lt.JPG)

