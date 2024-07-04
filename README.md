# Transformation Equation Project
This project contains the data analysis pipeline for observatory data, focusing on transforming RGB filter system data to the BVR filter system. The project includes files with steps such as filtering files, data extraction, zero-point adjustment and fitting transformation equation.

## Installation
1. **Clone the repository:**
    ```bash
    git clone https://github.com/amavicpos/transformation-eq-project.git
    cd transformation-eq-project
    ```
2. **Ensure you have Microsoft Visual C++ 14.0 or greater and pip install all the dependencies**

## Usage
-   **For the non _march data (such as the example data folder), for each sky:**
    1. **Run adjust_files.ipynb**
    2. **Run photometry.ipynb**
    3. **Run zeropoint.ipynb**
    4. **Run fitting.ipynb**
-   **For the _march data:**
    1. **Run rename_march.ipynb**
    2. **Run photometry_march.ipynb**
    3. **Run zeropoint_march.ipynb**

## Pipeline Steps
There was data that was in both RGB and BVR systems and data that was only in RGB. The data available only in RGB were images centred at RZ Cep and at TYC 3023-1974-1, in units of counts. The data available in both systems were images centred at various coordinates, in units of flux. Also, the data that was in both systems had to be checked for saturation and the pixels were in different units to the data that was only in the RGB system. The zero-point adjustment was slightly different in both as well. For the RZ Cep data, colour values and known magnitude values were added to get the transformation coefficients. Like that, the process was divided in seven files in total.

The data contained saturated images, images with weighted means magnitudes and images with the Luminance filter. All those files were removed to be able to process the data correctly, since they were not useful for the work. The renaming was done to extract useful information from filenames for the processing. Some of this information was also in the header of the .fits files, but not all, and some of it was easier to extract from the filenames themselves, such as the band in certain cases.

The data extraction and photometry pipeline did the following: detect stars and optionally plot the images with the sources circled, measure fluxes, identify sources (astrometry) and store some properties of the source in a database. For each pair of coordinates, the identified source was the brightest one in the aperture radius, which surpassed the detection threshold. For the red (RGB) band in the RGB-BVR group, there was no known source with a (known) magnitude value in SIMBAD, the reference database, so for each pair of coordinates, the chosen source was the closest one, this was set to be like that for any source that did not have any identifiable source with known magnitude in the RGB-BVR system dataset. The background subtraction was done with _sep_ by taking the average of the image before detecting the sources. But for measuring fluxes, a more accurate method was used, using an annulus around the aperture and subtracting the contribution of the detected flux in it to the flux measured in the aperture. The resulting table contained the names of the sources found in SIMBAD. After each image iteration, it was check that there was only one pair of coordinates assigned to each source name. Some of the sources where browsed manually with their measured coordinates and the names were the assigned ones.

The zero-point adjustment was done by taking a reference date time included for each band, the one with most detected sources, and for each other image, calculating the mean difference in magnitude for the sources that appear in both frames. The standard deviation of that difference was also calculated. That average difference in magnitude was then added to the measured magnitude for each frame. A reference date with time was used instead of a reference day because there were not enough nights to compare.

The coefficients of the transformation equation were calculated separately for each band by a linear fit with _curve\_fit_ from _scipy_, with the corresponding colour values and standard magnitude value from SIMBAD.
