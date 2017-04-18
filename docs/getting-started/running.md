## Basics

After a successful installation you will find `nuwro` executable in `nuwro/bin`
folder. 

!!! hint
    You can run NuWro providing the path to `nuwro`
    or you can add `nuwro/bin` to `$PATH` by adding the following line
    to your `~/.bashrc`:

        export PATH=(path to nuwro)/bin:$PATH

NuWro does not require any command line arguments, although there are several
optional arguments:

* `-o` to provide the path for output files
* `-i` to provide the path to an input file with simulation settings
* `-p` to change a single simulation parameter w.r.t to the input file
(this one can be use multiple times)

In general, the command to run NuWro would look like this:

    nuwro [-o output root file] \
          [-i input parameters file] \
          [-p "parameter name 1 = value 1"] \
          [-p "parameter name 2 = value 2"] \
          ...
          [-p "parameter name N = value N"]

!!! warning ""
    When running RES and DIS channel you will see many PYTHIA warnings about
    catching an infinite loop - just ignore them. 

### Input

The path to a simulation settings file can be provided with `-i` option.

!!! note
    If the path to a simulation settings file is not provided:
    
    * `$PWD/params.txt` will be used or
    * `nuwro/data/params.txt` will be used or
    * hard-coded default settings will be used.

The example settings file can be found in `nuwro/data/params.txt`. 
It is recommended to copy this file to the working directory
and modify as needed.

The list of available settings can be found in [parameters section](parameters).

!!! note "Example"
    Copy default settings files to your working directory:
    
        cp /path/to/nuwro/data/params.txt .
        
    modify as needed and run NuWro:
    
        nuwro
        
    *Note: If `nuwro/bin` is not in `$PATH` you need to provide the full path.*
    
!!! note "Example"
    Copy default settings files:
    
        cp /path/to/nuwro/data/params.txt /some/path/my_params.txt
        
    modify as needed and run NuWro providing the path to `my_params.txt`:
    
        nuwro -i /some/path/my_params.txt
        
Parameters from a settings file can be modified using `-p` option.
It is useful if you need to perform several simulations with only small changes
to settings.

!!! Example
    Let `/some/path/my_params.txt` be you base configuration.
    The following bash script:
    
        #!/bin/sh
        for Ma in $(seq 1000 100 1300)
        do
            nuwro -i /some/path/my_params.txt \
                  -o my_output_$Ma.root \
                  -p "qel_cc_axial_mass = $Ma"
        done
        
    will perform 4 simulations with the same settings but axial mass.

### Output

NuWro produces three output files:

* `$PWD/eventsout.root` with an events tree
* `$PWD/eventsout.root.txt` with flux-integrated cross sections
* `$PWD/eventsout.root.par` with parameters used for the simulation

The output path can be changed with `-o` option.

!!! note "Example"
    The following command:
    
        nuwro -o /path/to/simulation.root
        
    will produce the following output files:
    
    * `/path/to/simulation.root`
    * `/path/to/simulation.root.txt`
    * `/path/to/simulation.root.par`
    
The instruction on how to work with NuWro output files can be found [here](/analyze-output/basics).

## Examples

The list of all available settings
(as well as instruction on how to define beam and target)
can be found in [parameters section](parameters).

This section is just to demonstrate basic usage.


### CCQE on Carbon

We want to perform the following simulation:

* muon neutrino
* mono-energetic beam 1 GeV
* target = Carbon
* only CCQE dynamics

Lets start with grabbing the default settings file,
which we are going to modify for our needs:

    cp /path/to/nuwro/data/params.txt ccqe_carbon.txt
    
#### 1. Setting the beam

Locate "Beam specification" section and make sure
that all beam related settings are commented (including includes `@beam/...`).

Add the following lines to define the beam:

    beam_type = 0
    beam_particle = 14
    beam_energy = 1000
    
#### 2. Setting the target

Locate "Target specification" section and make sure
that all target related settings are commented (including includes `@target/...`).

Add the following lines to define the target:

    target_type = 0
    nucleus_p = 6
    nucleus_n = 6
    
!!! note "Predefined targets"
    Instead of defining target by hand it is possible to use predefined targets
    from `nuwro/data/target/` folder. In this case simply add:
    
        @target/C.txt

#### 3. Setting dynamics

Locate the following lines:

    dyn_qel_cc = 1  // Quasi elastic charged current
    dyn_qel_nc = 0  // Quasi elastic neutral current
    dyn_res_cc = 0  // Resonant charged current 
    dyn_res_nc = 0  // Resonant neutral current
    dyn_dis_cc = 0  // Deep inelastic charged current 
    dyn_dis_nc = 0  // Deep inelastic neutral current 
    dyn_coh_cc = 0  // Coherent charged current
    dyn_coh_nc = 0  // Coherent neutral current
    dyn_mec_cc = 0  // Meson exchange charged current
    dyn_mec_nc = 0  // Meson exchange neutral current

and modify according to your needs. In this case disable all dynamics but CCQE.

#### 4. Running simulation

If the `ccqe_carbon.txt` settings file is ready simply run:

    nuwro -i ccqe_carbon.txt -o ccqe_carbon.root
    
### Comparing nucleus models

We want to compare three nucleus models implemented in NuWro:

* (global) Fermi gas (with fixed Fermi level)
* local Fermi gas (with Fermi level dependent on density)
* spectral function

Lets consider the NuMi neutrino beam and CH target for all CC channels.
We need to perform three simulation, but they all may share the base settings file:

    cp /path/to/nuwro/data/params.txt numi_cc_ch.txt

#### 1. Setting beam and target

Make sure that all other beam and target settings
("Beam specification" and "Target specification") are commented.
Then add the following lines to `numi_cc_ch.txt`:

    @beam/NuMi_nu_100_10000.txt  # predefined beam from nuwro/data/beam/
    
    target_type = 1              # composed target
    target_content = 6 6 1x      # 1 x Carbon
    target_content += 1 0 1x     # 1 x Hydrogen

#### 2. Setting dynamics

Locate the following lines:

    dyn_qel_cc = 1  // Quasi elastic charged current
    dyn_qel_nc = 0  // Quasi elastic neutral current
    dyn_res_cc = 1  // Resonant charged current 
    dyn_res_nc = 0  // Resonant neutral current
    dyn_dis_cc = 1  // Deep inelastic charged current 
    dyn_dis_nc = 0  // Deep inelastic neutral current 
    dyn_coh_cc = 1  // Coherent charged current
    dyn_coh_nc = 0  // Coherent neutral current
    dyn_mec_cc = 1  // Meson exchange charged current
    dyn_mec_nc = 0  // Meson exchange neutral current
    
and modify according to your needs. In this case turn on only CC interactions.

#### 3. Running simulations

As we are going to change only nucleus model we may use the same base settings
file for all three simulations.

For (global) Fermi gas you have to set `nucleus_target = 1`
and you may want to make sure that spectral function is disabled (`sf_method`):

    nuwro -i numi_cc_ch.txt \
          -p "nucleus_target = 1" \
          -p "sf_method = 0" \
          -o numi_cc_ch_gfg.root
          
In the similar way you can perform the simulation for local Fermi gas:

    nuwro -i numi_cc_ch.txt \
          -p "nucleus_target = 2" \
          -p "sf_method = 0" \
          -o numi_cc_ch_lfg.root

and for spectral function:

    nuwro -i numi_cc_ch.txt \
          -p "nucleus_target = 2" \
          -p "sf_method = 1" \
          -o numi_cc_ch_sf.root

!!! note ""
    Spectral function is applied only for (quasi-)elastic dynamics.
    For others the model set by `nucleus_target` is used.
    
