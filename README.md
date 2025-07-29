# Quantification of tubulin co-sedimentation data

## Installation

First, get the source code by cloning the repository

```shell
git clone https://github.com/gt-biomodel/tubulin-co-sedimentation-plots.git
cd tubulin-co-sedimentation-plots
```

Then, you need to create a conda environment in which to install the required libraries. You can do that via pip or using the environment YAML file (see below).

### Using environment.yml

Create a conda environment with the required libraries using the YAML file:

```shell
conda env create -f environment.yml
```

This command should create an environment called "pelleting".

### Using pip

Alternatively, you can first create a conda environment and then install the dependencies via pip:

```shell
conda create -n  "pelleting" python=3.9.12
pip3 install -r requirements.txt
```

The following libraries should be installed:

- matplotlib>=3.4.3
- numpy>=1.22.3
- pandas>1.2.3
- scipy>=1.8.1
- openpyxl>=3.1.2

## Pre-requisites

1. Run SDS-PAGE gel for supernatant and pelleted fraction for all the tubulin concentrations used in the co-sedimentation assay for each replica.
2. Create a folder for each replica (can be named as you like but requires a number at the end).
3. Perform the gel densitometry with the Fiji software [1] to quantify the intensity of the band of interest both in the pelleted fraction and the supernatant. Save the results of the quantification as CSV files in a subfolder named `quantification` (case insensitive) inside the corresponding replica folder. In case you work with a protein complex and you wish to perform the quantification of multiple proteins, you have to append an identifier of the band (typically the name of the protein corresponding to the band of choice) Example: `quantification_p1`.
4. **Note on the naming of the CSV-files:** Typically, two SDS-PAGE gels are necessary for each replica. Thus, this script expect two CSV-files, which should be saved with the following name structure: `replica<nr>_0pos<array-of-lane-positions-with-0-intensity>_low.csv` (for the gel with the lower range of tubulin concentrations) and `replica<nr>_0pos<array-of-lane-positions-with-0-intensity>_high.csv` (for the gel with the higher range of tubulin concentrations). Each lane position in which no intensity for the band of interest was detected should be separated by a `-`. If no position has zero intensity, you should place a `-` anyway. The numbering of the lane position start from 1. Moreover, the pellet and supernatant fraction are alternated, with the supernatant first followed by the corresponding pellet fraction.

### Notes on the Excel file

The Excel file is required to define the color and legend for each sample in the plot and which tubulin concentrations to use for the quantification. Each sheet should be named exactly as the folder used for the sample under analysis. If you analyzed different versions for that sample (e.g. wild-type nd mutants), use one column for each of them in the corresponsing Excel sheet. The column should have the "contruct" entry named exactly as the subfolder used to store the data (including the `quantification` folder) for the sample variant of interest. You can refer to the template provided in this repository as reference to fill. <br><br>
Example of the folder structure:

```
sample-type/
|__ construct/
    |__ replica<nr>/
        |__ quantification/
            |__ CSV files
```

## Running script to plot data

First, you have to activate the conda environment:

```shell
conda activate pelleting
```

To print out the help section, you can run the following command from the terminal:

```shell
python plot_cosedimentation.py --help
```

The required inputs are a list of paths to the folders storing the data for each construct of interest (specified in the `--samplePath` argument) and the path to the Excel file with the details required for the plot for each construct (i.e. color, name to use in the legend, tubulin concentrations to use; specified in the `--pathToExcel` argument). A template .xlsx file is available in the repository.<br>
Additional parameters that can be specified are the following:

- `--Kd`: If this is passed, then the estimated K<sub>d</sub> value will be printed in the legend;
- `--Kd_round_digits`: How many digits to round the estimated K<sub>d</sub> value, default is 1. Only for the plot legend;
- `--ext`: File type you wish for saving the plot. Available options: 'svg','png','pdf','tiff', 'jpg', 'jpeg'; the default is PNG.

### Usage exaples

- Plot two constructs, save plot as .png, no K<sub>d</sub> indicated in the legend:

```shell
python plot_cosedimentation.py --samplePath path/to/sample_1/construct_1/ path/to/sample_1/construct_2/ --pathToExcel constructs_spec.xlsx
```

- Plot two constructs, save as svg, indicate K<sub>d</sub> in the legend rounded to 1 digit:

```shell
python plot_cosedimentation.py --samplePath path/to/sample_1/construct_1/ path/to/sample_2/construct_1/ --pathToExcel constructs_spec.xlsx –Kd –ext svg
```

### Outputs

- Image with the plot of the mean values of the tubulin co-sedimented fraction for each MAP against tubulin concentration, the error bars corresponding to the standard deviations and the fitted curve.
- CSV file with the estimated dissociation constant (K<sub>d</sub>; in &mu;M) and its standard deviation, the estimated maximal bound fraction and its standard deviation, and the R<sup>2</sup> of the fitted model for each construct under analysis.

The output files are saved in a user-defined folder that has to be defined by typing the path in the terminal once prompted by the script. If only one construct is quantified, then the output files are saved in the folder that is passed as input.

## References

[1] Schindelin, J. et al. Fiji: an open-source platform for biological-image analysis. Nat. Methods 9, 676 (2012). [https://doi.org/10.1038/nmeth.2019](https://doi.org/10.1038/nmeth.2019)