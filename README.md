<h1 align="center">
  pyLMP
</h1>
<p align="center">This repository enables users to perform molecular dynamics simulations utilizing LAMMPS or GROMACS. It can either start from given topology and coordinate file, or create them itself by using moleculegraph and supplementary Python code. YAML files are utilized to parse simulation settings, such as ensemble definitions, sampling properties and system specific input.  </p>

Online documentation is available at: https://samirdarouich.github.io/MDSetup

## 🚀 Getting Started

Get started by running the following command to install:

1. MDSetup
```
git clone https://github.com/samirdarouich/MDSetup.git
cd MDSetup
pip install .
```


## 🐍 Example program

# MDSetup

This module enables users to setup molecular dynamics simulations utilizing LAMMPS / GROMACS. Either a topology and system file can be presented, or moleculegraph and suplementary Python code combined with a force field provided as toml/json file can be utilized to create these.

1) Read in the YAML files to define the system and simulation/sampling settings. The software for which the input should be generated is defined in the 'setup.yaml'.

```python
lammps_setup = MDSetup( system_setup = "input/setup.yaml", 
                        simulation_default = "input/defaults.yaml",
                        simulation_ensemble = "input/ensemble.yaml",
                        simulation_sampling = "input/sampling.yaml",
                        submission_command = "qsub"
                      )
```

## Setting up a simulation pipeline

In this section the possibility to setup a simulation folder, along with a simulation pipeline using several ensembles, is provided.

1) Setup simulation and build initial system (if not provided)

```python
# Define the simulation folder
simulation_folder = "md_thermo"

# Define the ensembles that should be simulated (definition what each ensemble means is provided in yaml file)
ensembles = [ "em", "npt" ] 

# Define the simulation time per ensemble in nano seconds (for em the number of iterations is provided in the ensemble yaml)
simulation_times = [ 0, 10.0 ]

# Define initial systems, in case the simulation should be continued from a prior simulation.
# In that case, provide one initial structure for each temperature & pressure state.
# If the simulation should start from an initial configuration, provide an empty list.
initial_systems = [ "example/lammps/liquid_systems/structures/butane_hexane.data" ]

# Provide kwargs that should be passed into the input template directly
input_kwargs = {  }

# Define number of copies
copies = 2

# Define if the inital system should build locally or with the cluster
on_cluster = False

# Define the starting number for the first ensemble ( 0{off_set}_ensemble )
off_set    = 0

lammps_setup.prepare_simulation( folder_name = simulation_folder, ensembles = ensembles, simulation_times = simulation_times,
                                 initial_systems = initial_systems, input_kwargs = input_kwargs, copies = copies,
                                 ff_file = ff_file, on_cluster = on_cluster,  off_set = off_set 
                                )
```

2) Submit jobs to cluster

```python
# Submit the simulations
lammps_setup.submit_simulation()
```
## Extract sampled properties

This section analyses the simulation output in the specified ensemble and the provided properties. Each copy of the system is analysed and an average is generated. This 
is finaly saved in a resulting json file.

```python
# Extract properties from LAMMPS and analyse them

# Define analysis folder
analysis_folder = "md_thermo"

# Define analysis ensemble
ensemble = "01_npt"  

# Properties to extract
properties = ["temperature", "potential energy", "kinetic energy", "enthalpy"]

# Suffix of output file
output_suffix = "energy"

# Percentage to discard from beginning of the simulation
fraction = 0.25

lammps_setup.analysis_extract_properties( analysis_folder = analysis_folder, ensemble = ensemble, 
                                          extracted_properties = properties, output_suffix = output_suffix, 
                                          fraction =  fraction 
                                        )
```


## 👫 Authors

Samir Darouich - University of Stuttgart

## 📄 License

This project is licensed under the MIT License - see the LICENSE.md file for details
