# Public_HBP: Repository for "The Economics of Time-Limited Development Options: The Case of Oil and Gas Leases"

## Repo and dropbox organization
- All of the code for this project is stored in this repository. To run the code, you need to clone this repo to your local machine (or copy the files directly, preseving the subfolder structure).
  * The local folder holding the repository is referred to as `codedir` below.
  * The repo uses git large file storage (LFS), and accordingly contains a .gitattributes file
  * In addition to the code, this repo also holds the .tex and .pdf file of the paper itself, along with all supporting figures, tables, and single-number .tex input files
- Data files are available via Dropbox [here](https://www.dropbox.com/sh/62uglplql0iyquz/AABktNlcOl3ecvl6f1natATea?dl=0)
  * The `RawData` subfolder contains the raw data for the project that we are able to post publicly.
    - Each subfolder of `RawData` contains a `README.md` file that provides information on how we originally accessed each raw data file.
    - We have posted all raw data except proprietary data that we obtained from Enverus (formerly DrillingInfo). The Enverus data include all information on leases, wells' monthly production, and drilling rig dayrates.
      * The `README.md` files point to the Enverus products we used to obtain the data and when we obtained them. Enverus-authorized users should note that current versions of these files that you obtain directly from Enverus may differ from the versions that we obtained. We would be happy to share our original Enverus raw data with other Enverus-authorized users.
  * The dropbox folder also includes an intermediate (`IntermediateData`) data folder that is populated with cleaned, merged versions of the raw data, as well as inputs to the computational model.
    - We have included in `IntermediateData` all files that do not include identifiable information from Enverus.
      * `IntermediateData/PriceDayrate/PricesAndDayrates_Quarterly.csv` has had the Enverus dayrates in column 5 replaced with the minimum and maximum dayrates that we observe in our data (these dayrates are used to define the model's state space). The dayrate for 1Q 2010 -- the calibration date for the model -- is the only dayrate that is correct.
  * The dropbox folder also includes an `Images` folder that includes images that are not generated by our code but are used in slide presentations.
  * Users should download the `RawData`, `IntermediateData`, and `Images` folders together into a single folder. This folder is referred to as `dropbox` below.


## Software requirements
The code in the repo requires Stata, R, Python, Matlab, a LaTex compiler, and a bash shell.
- Users who only want to use the publicly available data to run the simulations (the scripts called in `HBP/Work/Code/Analysis/Model/model_sim_script.sh`) only need R, Matlab, and bash.
  * Users can get away with only Matlab if they only want to directly run the Matlab simulation scripts in `model_sim_script.sh`.


### Stata
- All Stata scripts have been verified to run on Windows OS 64-bit Stata MP v15. (SE should work fine provided users specify the bash scripts correctly; see below.)
- Required package installations are included as part of the bash script (see below)
- To run the Stata code, you need a file called `globals.do` stored in your local root HBP repo folder. (`globals.do` is .gitignored)
    - `globals.do` should look like the below, pointing to your own repo and dropbox directories:
```
global hbpdir = "C:/Work/HBP"
global dbdir = "C:/Users/Ryan Kellogg/Dropbox/HBP"
```



### R
- All R scripts have been verified to run on Windows OS 64-bit R v3.5.1.
- Required package installations are included as part of the bash script (see below)
- To run the R code, you need files called `paths.R` and `data.R` stored in your local root HBP repo folder. (both `paths.R` and `data.R` are .gitignored)
    - `paths.R` should look like the below, pointing to your own repo and dropbox directories:
```
repo <-
  file.path("C:/Work/HBP")
dropbox <-
  file.path("C:/Users/Ryan Kellogg/Dropbox/HBP")
```

    - `data.R` must match the below:

```
source(file.path(root, "paths.R"))

fdir <- file.path(repo, "Paper/Figures")
featherdir <- file.path(repo, "ClusteringData")
```


### Python
- The Python script (there is only one) has been verified to run on Windows OS 64-bit Python 3.7.
- There are two packages---fuzzywuzzy and feather-format---that you must install before running the bash script. It is recommended you use [Anaconda](https://conda.io/docs/user-guide/install/download.html) to install these packages.
- Package import statements using Anaconda:
```
conda install -c conda-forge fuzzywuzzy
conda install -c conda-forge feather-format
```
- To run the Python script, you need to update the directory names at the start of the file. The file is HBP/Code/Build/Louisiana/downweighting_leases/string_dissim.py.
  * The directories should look like the below, pointing to your own repo and dropbox directories:
```
dbPath=r"C:/Users/Ryan Kellogg/Dropbox/HBP"
featherPath = r"C:/Work/HBP"
```


### Matlab
- All Matlab scripts have been verified to run on Windows OS 64-bit version R2019b.
- Before running the m-files, users must install the following toolboxes:
  * Statistics and Machine Learning Toolbox
  * Deep Learning Toolbox
  * Optimization Toolbox
  * Econometrics Toolbox
  * Signal Processing Toolbox
- To run the Matlab code, you need a file called `globals.m` stored in your local root HBP repo folder. (`globals.m` is .gitignored)
    - `globals.m` should look like the below, pointing to your own repo and dropbox directories:
```
repodir = 'C:/Work/HBP';
dropbox = 'C:/Users/Ryan Kellogg/Dropbox/HBP';
```
- Note that the computational model is built in Matlab using object-oriented programming. The model objects are defined in hbpmodel.m, hbpmodelsim.m, and hbpmodelwaterest.m. hbpmodel.m is the superclass. The hbpmodelsim.m subclass is used for simulating counterfactuals, and the hbpmodelwaterest.m subclass is used for calibrating the water price P_w.
  * If you are unfamiliar with object-oriented programming in Matlab, see resources available [here](https://www.mathworks.com/discovery/object-oriented-programming.html)
  * The other m-files in HBP/Code/Analysis/Model instantiate model objects using one or more of the above classes to carry out calibration and simulation.


### LaTex
- A full LaTex build is required to compile the paper.



### Bash
#### Description
- The repo includes a master bash script, `hbp_bash_file.sh`, located in the root repo directory. For users with access to the full set of raw data in `Dropbox/HBP/RawData` (including the proprietary Enverus data), executing this script will conduct all of the data work and analysis, and compile the paper.
- The main hbp_bash_file.sh call seven sub-scripts to do the work:
  1. `HBP/prelim1_installs.sh`. Installs required Stata and R packages.
  2. `HBP/prelim2_folders.sh`. Deletes intermediate data files in `Dropbox/HBP/RawData/data` (true raw data in `RawData/orig` are preserved) and in `Dropbox/HBP/IntermediateData`. Deletes results in `HBP/Paper/Figures`. Copies images from `Dropbox/HBP/Images` to `Dropbox/HBP/Figures`.
  3. `HBP/Code/Build/build_script.sh`. Imports and cleans all raw data from `Dropbox/HBP/RawData/orig`. Spatially merges wells to units. Clusters leases into units.
      * The clustering of leases into units is the expensive part of this call and takes roughly half a day to run.
  4. `HBP/Code/Analysis/Louisiana/analysis_script.sh`. Computes summary statistics and conducts the bunching analyses. Outputs maps for the paper. Outputs statistics that feed into the computational model.
  5. `HBP/Code/Model/model_cal_script.sh`. Calibrates the computational model. Outputs calibrated parameters and figures showing model fit to data. Requires several hours to run.
  6. `HBP/Code/Model/model_sim_script.sh`. Computes the optimal royalty and primary term, along with all other counterfactuals. Outputs simulation results and figures. Requires roughly a day to run.
  7. `HBP/Paper/paper_script.sh`. Compiles the paper.
- `HBP/Code/Model/model_sim_script.sh` and `HBP/Paper/paper_script.sh` can be executed using the publicly available data in the [dropbox folder](https://www.dropbox.com/sh/62uglplql0iyquz/AABktNlcOl3ecvl6f1natATea?dl=0). 
  * The dayrates that the model uses as input, located in `Dropbox/HBP/IntermediateData/PriceDayrate/PricesAndDayrates_Quarterly.csv` are not the true dayrates in our public data. The only correct dayrate in this file is that for 1Q 2010, the calibration date of the model. The file also includes the minimum and maximum dayrate that we observe in our data (these dayrates are used to define the model's state space).
- The sub-bash scripts can be run independently if the user only wants to run part of the code.
  * Though once `HBP/prelim2_folders.sh` is run, the only script that will function is `HBP/Code/Build/build_script.sh`.


#### Running the bash scripts
- Apple and Linux users have bash automatically built into their OS and do not need to install it
- Windows users can access a bash shell by installing git and using the git command line
  * Be sure to open git with administrative privileges
- To run the Stata, R, and Python scripts using the bash scripts, Windows users may need to add the following lines to their PATH environment (exact paths may vary depending on the Stata and R versions you have installed; access the PATH environment by going to Control Panel --> System --> Advanced System Settings --> Advanced Tab --> Environment Variables... --> Path --> Edit...):
  * C:\Program Files (x86)\Stata15
  * C:\Program Files\R\R-3.5.1\bin\x64
  * C:\ProgramData\Anaconda3
  * C:\ProgramData\Anaconda3\Scripts
  * C:\ProgramData\Anaconda3\Library\bin
- Mac OS users can ensure that their version of Stata is included in their PATH by executing one of the following commands via bash 
```
sudo ln -s /Applications/Stata/StataMP.app/Contents/MacOS/stata-mp /usr/local/bin
sudo ln -s /Applications/Stata/StataSE.app/Contents/MacOS/stata-se /usr/local/bin
```
- Mac users can do the same with their version of Matlab (we have found that Windows users do not need to add Matlab to their PATH variable). 
  * To find your Matlab application path name, navigate to opening Matlab and type `matlabroot` in the terminal. You can then link Matlab to your $PATH using the following command:
```
sudo ln -s /Applications/MATLAB_R2018b.app /usr/local/bin
```
- In order to run the bash scripts, you may need to give your machine the correct permissions. Do so with the following command and the correct path to script for your script:
```
chmod +x /path/to/your/script.sh
```
- You need to specify at least one of the `CODEDIR`, `DBDIR`, `OS`, and `STATA` variables in each of the bash scripts in the list below. These point to the local root repo directory, dropbox directory, operating system, and stata version
  * `CODEDIR` should look something like `CODEDIR=$HOME/Economics/Research/HBP`
  * `DBDIR` should look something like `DBDIR="C:/Users/Ryan Kellogg/Dropbox/HBP"`
  * `OS` should be `OS="Windows"` or `OS="Unix"` (MacOS users should use the Unix version)
  * `STATA` should be `STATA="MP"` or `STATA="SE"`
    - Note: do NOT include a white space on either side of the equal sign in any of these expressions
  * To identify your `$HOME` variable, you can type `echo $HOME` into your bash shell command line
  * Bash scripts that need at least one of `CODEDIR`, `DBDIR`, `OS`, or `STATA` updated are as follows:
    - `hbp_bash_file.sh`
    - `prelim1_installs.sh`
    - `prelim2_folders.sh`
    - `build_script.sh`
    - `analysis_script.sh`
    - `model_cal_script.sh`
    - `model_sim_script.sh`
    - `paper_script.sh`
      * In each of the above, update only the variables that are already defined in each script. e.g., if a script does not define `DBDIR`, you do not need to add it.
- We recommend running the scripts by opening your bash shell, changing the directory to your local repository, and then using the following command
```
bash -x hbp_bash_file.sh |& tee hbp_bash_file_out.txt
```
  * This command will log output and any error messages to `hbp_bash_file_out.txt`