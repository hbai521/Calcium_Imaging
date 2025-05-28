Calcium Imaging data analysis on Drosophila larval thermal sensing neurons

Overview

This repository provides Python scripts and a complete data analysis pipeline for 3D calcium imaging of thermosensory neurons in Drosophila larvae. Designed as an instructional resource, this pipeline offers step-by-step guidance for students and educators to analyze real calcium imaging datasets with minimal coding experience.

This pipeline is used to:

- Extract neuron fluorescence intensities from Z-stack TIFFs using TrackMate in Fiji/ImageJ.

- Performing background correction, and running quantitative analysis to Calculate ΔF/Fmin across time.

- Align calcium activity with temperature profiles.

- Visualize average responses.

Requirements

### 1.  ImageJ (Fiji): 

### 2. Python: 

Python libraries: pandas, matplotlib, numpy

To install dependencies: 


```
bash

Pip install –r requirements.txt

Setup: Download Repository & Set Up Python Environment

Before starting the analysis, follow these steps to set up your Python environment and download the necessary scripts:

## 1. Setup a Python Virtrual Environment

You can either use the terminal or create a new project in PyCharm.

### Terminal Setup


```
bash

python -m venv calcium_env

# Active the environment:

source calcium_env/bin/activate      # On macOS/Linux

calcium_env\Scripts\activate         # On Windows

```


### Terminal Setup

Open PyCharm → New Project

Select "New environment using venv"

Set the folder name (e.g., calcium_env)

Click Create

## 2. Install Required Packages

Install dependencies using pip:


```
bash

pip install -r requirements.txt

```


This will install essential packages like pandas, numpy, and matplotlib.

## 3. Clone or Download This Repository into the virtual environment/project folder

### Clone with Git


```
bash

git clone 

```


### Download ZIP

Click the green "Code" button on this GitHub page

Select "Download ZIP"

Unzip the folder into your calcium_env (or PyCharm project) directory

Input File Organization

Before starting the analysis, unzip demo data and make sure the files and folder structure match the following:

project/

│

├── MAX_Samplexxx.tif            # Maximum projection TIFF for initial visualization

├── Analog - <timestamp>.csv      # Temperature log recorded during imaging

├── Samplexxx_stacks/           # 21 z-slice TIFFs (Z01 to Z21), each with 88 time points

│   ├── Z01.tif

│   └── ...

├── Analysis/       #empty folder with correct file structure 

     ├── background_i.xlsx       

     ├── Neuron 0/   

    └── Neuron 1/     

Notes: Keep file names and folder structure exactly as recommended. The analysis scripts rely on this structure to locate and process data correctly. Incorrect naming or layout will likely result in file-not-found or parsing errors during execution.             

Workflow

## Step1: Visualizing Neuron Response in ImageJ

(add overview)

### Why start with the maximum projection?

Starting with the **maximum projection image** helps you quickly access:

An overview of neuron activity across time frames, such like Fluorescence intensity and response to the stimuli

The number, shape, position of neurons in the sample

Easier to interpret than raw Z-stack TIFFs because it flattens all Z-planes into one image.  

It serves as a first-hand visual screen for movement artifacts before conducting in-depth individual z-slice analysis.

Large movement prevents TrackMate tracking, and manual extraction of intensity values will be necessary.

Adding annotations and screenshots is strongly recommended. It helps track neuron position for later individual z-slice analysis. 

### Loading the Image in Fiji

1. Launch Fiji (ImageJ)

2. Drag and drop MAX_DOWCXXX.tif into the Fiji window, or use File → Open... and select MAX_DOWCXXX.tif.

3. Use the slider at the bottom to scroll through time frames.  

4. Adjust brightness Use Brightness/Contrast or Ctrl + Shift + C on windows Command + Shift + C on macOS.

## Step 2: Extracting Fluorescence Intensity Using TrackMate

### Overview

This step use TrackMate in Fiji to extract fluorescence intensity over time for each neuron from selected z-slice TIFFs. We will use Neuron 0 as example to demonstrate z-slice analysis in the demo dataset. 

### 1. Find the brightest z-slice in Fiji

Go to ‘SampleXXX_stack/’ folder and open a mid-plane TIFF (e.g. DOWC_Z10.tif)

Adjust brightness/contrast to see cells clear.

Find a strong response time point (e.g. t=42 in demo sample)

Open neighboring z-slices at t=42 and compare fluorescence brightness.

Select the strongest slice to start the analysis

> - For example:

> - Neuron 0, start the analysis on ##_z08.tif

> - Neuron 1, start the analysis on ##_z10.tiff	

### 2. Lauch TrackMate for Neuron 0

Open DOWCXXX_Z08.tif in Fiji

Go to **Plugin/Tracking/TrackMate**.

When prompted with "Z/T swapped?", click Yes.

### 3. Configure Detection Settings in TrackMate

Detector: Select DoG (Difference of Gaussian)

 Estimated object diameter: 22–36 pixels (based on neuron size)

Quality threshold: Start with 1.0 (reduce if needed)

Enable:

Sub-pixel localization

Pre-process with median filter

Click Preview to confirm that the detection (purple circle) fits the neuron correctly.

### 4. Apply Spot Filters

Use **‘Set filters on spots’** to remove irrelevant signals

Filter by **’X/Y position’** to isolate Neuron 0.

Use **Preview** to ensure only the correct neuron is selected.

Select Simple LAP tracker with following settings.

### 5. Export Intensity Data

Click Sports to acquire ‘All spots table’

Sort ‘All spots data ‘by clicking Track ID’

Click “Export to CSV” to save the file into Analysis/Neuron 0/Mean_Intensity08.csv.

### 6. Clean the Exported CSV file

**Notes: Accurate cleaning at this stage is essential for proper ΔF/Fmin calculation and downstream analysis in Python.**

Open the Mean_Intensity08.csv, remove extra headers

Manually check each row. Use  **‘Track ID’** and visualize same ID image in ‘Sport table’ in Trackmate. Remove any incorrect or off-target tracks in csv file. 

Sort the file by **’POSITION_T’**. 

Delete duplicates. 

Save the cleaned csv.

## Step 3: Background Correction

Before calculating ΔF/Fmin, five background fluorescence data must be recorded for each Z-slice used in analysis.

### 1. Open `background_i.xlsx` located in `Analysis/` folder.

### 2. Measure Background Fluorescence in Fiji

Go to t=12,24,36,48,60.

Hold shift and click the Oval tool in Fiji

Draw ‘circular ROI’ on a background area close to target neuron using the same diameter as neuron cell. 

Press M to obtain the intensity value

Record five background values at t=12,24,36,48,60 into the corresponding column of `background_i.xlsx`.

 

Copy and paste to background_i.xlsx.

**Important notes:**

The number of background values recorded must exactly match the number of Mean_Intensity##.csv files for each neuron.

For example, if Neuron 0 has cleaned data from Z05 to Z11, there should be seven sets of five background values for Neuron 0 in background_i.xlsx.

## Step 5: Work on the other stacks which you can see the target neuron.

Repeat the TrackMate analysis (Steps 2–4) for all Z-slices where the neuron is clearly visible.

## Step 6: Checklist Before Running Python

Make sure the following are true before running the analysis script:

### All extra headers are removed from every Mean_Intensity##.csv file

### No duplicate POSITION_T values exist in any Mean_Intensity##.csv

### background_i.xlsx contains a matching number of rows for each neuron's Z-slice .csv files

 Inconsistencies here will result in Python script errors or inaccurate ΔF/Fmin values.

## Step 7: Run `CIAanalysis_120min.py` to Calculate ΔF/Fmin

Once all intensity files and background data are prepared and validated, use the Python script `CIAanalysis_120min.py` to compute ΔF/Fmin for each neuron and generate visual outputs.

### 1. Open terminal or Pycharm

Navigate to your project directory and activate your Python environment (if applicable).

### 2. Command

Run the following command from your terminal or within your PyCharm terminal:


```
bash

python CIAnalysis_120min.py -i path/to/Analysis --merge --cell_type DOWC

```


**Arguments**

-i: Path to the Analysis/ folder

--merge: (optional) Combines results from all neurons into a summary

--cell_type: Adds a label (e.g., DOWC, DOCC) to outputs for reference

###3. Input Files before running the command

project/

│

├── MAX_SampleXXX.tif            # Maximum projection TIFF for initial visualization

├── Analog - <timestamp>.csv      # Temperature log recorded during imaging

├── SampleXXX_stacks/           # 21 z-slice TIFFs (Z01 to Z21), each with 88 time points

│   ├── Z01.tif

│   └── ...

├── Analysis/        # CIAanalysis_120min.py input

│   ├── background_i.xlsx       

│   ├── Neuron 0/

│   │   ├── Mean_Intensity##.csv      # file name should following the naming 

│   │   └── ...

│   ├── Neuron 1/

│   │   ├── Mean_Intensity##.csv      # file name should following the naming 

│   │   └── ...

###4. Output Files after running CIAanalysis_120min.py

After the script finishes, you will find the results in the following structure:

project/

│

├── MAX_SampleXXX.tif            # Maximum projection TIFF for initial visualization

├── Analog - <timestamp>.csv      # Temperature log recorded during imaging

├── SampleXXX_stacks/           # 21 z-slice TIFFs (Z01 to Z21), each with 88 time points

│   ├── Z01.tif

│   └── ...

├── Analysis/        #  CIAanalysis_120min.py input 

│   ├── background_i.xlsx

│   ├── background_list.csv       

│   ├── Neuron 0/

│   │   ├── Mean_Intensity##.csv      # file name should following the naming 

│   │   └── ...

│   ├── Neuron 1/

│   │   ├── Mean_Intensity##.csv      # file name should following the naming 

│   │   └── ...

│   └── results/       # generated results folder

│   │   ├──Neuron 0.csv       # ΔF/Fmin trace for Neuron 0

│   │   ├──Neuron 1.csv

│   │   ├──Neuron Plots/

│   │   │  ├──Neuron 0.png       #plot for neuron 0

│   │   │  └── Neuron 1.png

│   │   ├──Merged_data/

│   │   │  ├──merged_data.csv       #Combined ΔF/Fmin data

│   │   │  └── Average_dF.png

Notes: Filenames like ‘Mean_Intensity03.csv’ must follow the exact naming rule with the two-digit z-slice number for the script to parse them correctly

## Step 8: Run Align Calcium Signal with Temperature Using `CITbind_dynamic.py`

This step synchronizes the calcium activity (ΔF/Fmin) with the temperature log file collected during imaging. It generates a dual-axis plot that visualizes neuronal responses alongside temperature fluctuations over time.

---

### 1. Input Files before running the command

 Create a new folder named SampleXXX_date (e.g. DOWC001_20240820) 

Copy and paste in merged_data.csv from Analysis/results/merged_data.csv and temperature log

project/

│

├── MAX_SampleXXX.tif            # Maximum projection TIFF for initial visualization

├── Analog - <timestamp>.csv      # Temperature log recorded during imaging

├── SampleXXX_stacks/           # 21 z-slice TIFFs (Z01 to Z21), each with 88 time points

│   ├── Z01.tif

│   └── ...

├── Analysis/        #  

├──SampleXXX_cbind/        #  

│   ├── merged_data.csv

│   └── Analog - <timestamp>.csv

### 2. Command 

Run the following command in your terminal or PyCharm:


```
bash

python CITbind_dynamic.py -i path/to/sample_folder -n

```


**Arguments**

-i: Path to the folder that contains:

merged_data.csv

the corresponding temperature log file (e.g., Analog - 8-20-2024.csv)

-n: Number of neurons analyzed (i.e., number of individual neuron .csv files)

### 3. Output Files after running the command

The script will generate:

Overlaid plot of ΔF/Fmin and temperature vs. time

CSV file combining temperature and calcium signal data

├──SampleXXX_cbind/        #  

│   ├── SampleXXX.csv

│   ├── SampleXXX_cbind.csv

│   ├── SampleXXX_temp.csv

│   ├── SampleXXX_cbind0.pdf

│   └── SampleXXX_cbind1.pdf

After run the script you will get the results for the current sample. Then continue to work on all other samples and past all the results into a summary table. 

## Step 9: Average Calcium Signals Across Samples Using df_temp_class.py

This step computes the average ΔF/Fmin trace and generates group-level plots across multiple samples or neurons. This is useful for comparing replicates or summarizing class-wide data.

---

### 1. Required Input Files

Create a new folder named ‘summary’ two files in the same directory:

Summary_df.csv — calcium traces from all neurons

Summary_temperature.csv — corresponding temperature data for each sample

Make sure:

The first column name is Temperature, then continue with n1, n2, ... for summary_df.csv

The first column name is Temperature, then continue with t1, t2, ... for summary_temperature.csv

### 2. Command

Run the following command in your terminal or PyCharm:


```
bash python df_temp_class.py path/summary_df.csv path/summary_temperature.csv

```


### 3. Output

The script will generate:

Summary plot of the group average ΔF/Fmin trace with SEM (error bars)

Average trace CSV summarizing ΔF/Fmin values across all neurons

