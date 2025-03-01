# Anaconda / Python Environment Management

##Creating and Managing Anaconda Environments
```sh
conda create --name <name> python=<version> # Makes a new conda env 
conda activate my_env # Activates the env, IE uses it, also used to switch envs 
conda deactivate # Deactivates the current env
conda env list # Lists all conda environments 
conda remove --name my_env --all # Deletes an environment
conda info --envs # check the current env and path
```
## Installing Packages
- conda install installs from Anaconda repositories, ensuring package compatibility.
- pip install fetches packages from PyPI, which may have dependency issues with Conda environments.
```sh
conda install <name, ie numpy> <name2> # Installs to active conda env
pip install numpy matplotlib scipy # Installs with pip, the conda method is preffered 
```

## Importing and Exporting Conda env from/to a file
```sh
conda env export > environment.yml # The > writes the file 
conda env create -f environment.yml # Creates the environment from a file, use --name to name the env
```

## Magic jupyter notebook commands
Run in an empty jupyter-cell, jupyter knows your current active `python` or `conda` env and will install a package to that env. 
```sh
!pip install package_name  # For pip
!conda install package_name -y  # For conda
!which python # Path to check the currently active Python interpreter inside a Jupyter Notebook

```
