NuWro uses by default settings from the `params.txt` file located in `$PWD` directory.
If the file does not exist, the one from `nuwro/data` folder is loaded.
If both files are missing or some of the parameters are not set in the file,
default values are used. 

## General settings

| Parameter name | Possible arguments | Default value | Description |
|:--------------:|:------------------:|:-------------:|:-----------:|
| number_of_events | positive integer | 1e5 | The number of equally weighted events to be saved in the output ROOT file |
| number_of_test_events | positive integer | 1e6 | The number of events used to calculate cross sections (not saved by default) |
| user_events | 0, 1 | 0 | Used to turn on the fitting procedure:<br>0 - Run NuWro<br>1 - Fit axial mass to MiniBooNE data for CCQE |
| user_params <br> *(for user_events 1)* | x y z | - | Parameters for the axial mass extraction procedure:<br>x - the minimum axial mass value<br>y - the maximum axial mass value<br>z - the axial mass step |
| random_seed | positive integer | 0 | Controls the random seed persistence:<br>0 - use time(NULL) as a seed for the random number generator<br>1 - read state from “random seed” file or use seed=time(NULL), if the file was not found<br>n - use n as the seed for the random number generator |
| mixed_order | 0, 1 | 0 | If 1, events are saved to the output file in random order |
| save_test_events | 0-2 | 0 | Turn on to use test events in the analysis:<br>0 - test events are not saved<br>1 - test events are finalized and stored in weight.eventsout.root file, the average weight is equal to the total cross section<br>2 - test events of nonzero weights are finalized and stored in weight.eventsout.root file, the weights are respectively scaled, so the average weight is equal to the total cross section |

## Beam settings

| Parameter name | Possible arguments | Default value | Description |
|:--------------:|:------------------:|:-------------:|:-----------:|
| beam_direction | x y z | 0 0 1 | The direction of the neutrino momentum in xyz coordinates |
| beam_type | 0-4 | 0 | Types of beams:<br> 0 - a single neutrino flavor beam<br>1 - a mixed flavor beam<br>2 - a beam loaded from a ROOT file<br>3 - a beam loaded from the histogram (histout.txt)<br>4 - create histout.txt file based on a ROOT file (than use beam type 3 to run NuWro) |
| beam_particle<br>*(for beam_type 0)* | +/- 12, 14, 16 | 14 | PDG number of the incident neutrino |
| beam_energy<br>*(for beam_type 0)* | *explained below* | 1000 | The energy profile |
| beam_content<br>*(for beam_type 1)* | *explained below* | - | The mixed beam definition |
| beam_folder<br>*(for beam_type 2, 4)* | *path* | ../flux | The path to the directory with ROOT files |
| beam_file_first<br>*(for beam_type 2, 4)* | positive integer | 1 | The number of the first file in the folder to be read |
| beam_file_limit<br>*(for beam_type 2, 4)* | positive integer | 0 | The number of files to be loaded (0 - read files to the last one in the directory) |
| beam_offset | x y z | 0 0 0 | The offset of the position of the interaction in xyz coordinates |
| beam_placement<br>*(in cascade mode only)* | 0-2 | 0 | The starting position of the particle:<br>0 - the propagation starts at the center of the nucleus<br>1 - the propagation starts at a random place inside the nucleus<br>2 - the propagation starts just under the surface of the nucleus |

### Defining energy profile for a single flavor beam

For a single flavor neutrino beam you need to set up `beam_type = 0`
and specify neutrino PDG using `beam_particle = [PDG code]`.

The energy profile is set using `beam_energy`:

* for a mono-energetic beam use:

        beam_energy = E

    where `E` is neutrino energy in MeV

* for a uniform beam with energy range from `E0` to `E1` use:

        beam_energy = E0 E1
    
* for a non-uniform beam with energy range from `E0` to `E1` given by histogram
`a1, a2, ..., an` use:

        beam_energy = E0 E1 a0 a1 ... an

!!! hint
    The histogram does not have to be normalized. The probability of finding
    neutrino energy in i-th bin is defined as follows:
    
    $$P_i = \frac{a_i}{\sum\limits_{i=0}^n a_j}$$
    

!!! note "Example"

    The following code:

        beam_energy = 1000 2000 1 2 3 4

    defines the following beam:
    
        ^
        |
        |                       |-----|
        |                       |     |
        |                 |-----|     |
        |                 |     |     |
        |           |-----|     | 40% |
        |           |     | 30% |     |
        |     |-----| 20% |     |     |
        |     | 10% |     |     |     |   
        |-----|-----|-----|-----|-----|-----> E [MeV]   
            1000  1250  1500  1750  2000
         
### Defining energy profile for a mixed flavor beam

For a mixed flavor neutrino beam you need to set up `beam_type = 1`.

Instead of using `beam_particle` and `beam_energy` you should define a beam
with:

    beam_content = [PDG code] [fraction]% [beam energy]
    
where `[beam energy]` is defined as for a single flavor beam.

!!! note "Example"
    The following code:
    
        beam_content = 14 80% 1000
        
    defines mono-energetic muon neutrino beam,
    which constitutes 80% of the whole flux.

You may add as many beam contents as you want in the following way:

    beam_content += [PDG code] [fraction]% [beam energy]
    
!!! warning ""
    Please note `+=`. Using `=` would clear the previous content.
    
In general, a mixed flavor beam definition would look like this:

    beam_content = [PDG code 1] [fraction 1]% [beam energy 1]
    beam_content += [PDG code 2] [fraction 2]% [beam energy 2]
    beam_content += [PDG code 3] [fraction 3]% [beam energy 3]
    ...

!!! note "Example"
        
    The following code:
    
        beam content = 12 75% 1000
        beam content += -12 20% 1000 2000
        beam content += 14 5% 1000 1500 1 5 10 15 5 1

    defines the following beam:

    * 75% of mono-energetic electron neutrinos
    * 20% of electron anti-neutrinos with uniformly distributed energy
    * 5% of muon neutrinos with some energy distribution in range 1-1.5 GeV

### Predefined beams

One can also use predefined beam specifications instead of the above parameters.
The list of beams can be found in `nuwro/data/beam` directory.
To use one of those beams, one must use the following line:

```
@beam/beamfile.txt
```

where `beamfile.txt` is the name of the file from `nuwro/data/beam` directory.

## Target settings

| Parameter name | Possible arguments | Default value | Description |
|:--------------:|:------------------:|:-------------:|:-----------:|
| target_type | 0-2 | 0 | Types of targets:<br>0 - a single nucleus<br>1 - a target composed from
some nuclei<br>2 - a detector geometry loaded from a ROOT file |
| nucleus_p<br>*(for target_type 0)* | positive integer | 6 | The number of protons in a target nucleus |
| nucleus_n<br>*(for target_type 0)* | positive integer | 6 | The number of neutrons in a target nucleus |
| nucleus_E_b<br>*(for target_type 0)* | positive integer | 34 | The binding potential (sum of binding and Fermi energies) |
| nucleus_kf<br>*(for target_type 0)* | positive integer | 220 | The Fermi momentum |
| nucleus_target | 0-5 | 2 | Nucleus models used in a primary interaction:<br>0 - free nucleon<br>1 - Fermi gas<br>2 - local Fermi gas<br> 3 - Bodek-Ritchie model<br>4 - spectral function<br>5 - deuterium |
| nucleus_model | 0, 1 | 1 | Nucleus density profiles for FSI:<br>0 - constant density<br>1 - realistic density profile |
| target_content*(for target_type 1)* | *explained below* | - | The composed target definition |
| geo_file<br>*(for target_type 2)* | *filename* | *target/ND280v9r7p5.root* | The path to the file with the detector geometry |
| geo_name<br>*(for target_type 2)* | *geometry name* | *ND280Geometry v9r7p5* | The name of the geometry in the file |
| geo_o<br>*(for target_type 2)* | x y z | 0 0 0 | The coordinates of the center of the box |
| geo_d<br>*(for target_type 2)* | x y z | 2000 5000 2000 | The half dimension of the box |
| geo_volumne<br>*(for target_type 2)* | *master volume name* | - | The name of the *master volume* in the detector file |

### Defining a single nucleus target

In order to define a single nucleus target you need to set up `target_type = 0`.
Then, define the number of nucleons using:

    nucleus_p = [#protons]
    nucleus_n = [#neutrons]
    
You may set up other nucleus properties using parameters listed in the table above.

!!! note "Example"
    To define Carbon target use:
    
        target_type = 0
        nucleus_p = 6
        nucleus_n = 6
        
### Defining a composed target

In order to define a multi-nuclei target you need to set up `target_type = 1`.

Instead of using `nucleus_p`, `nucleus_n` and other nucleus related parameters
you should define your target with:

    target_content = [#protons] [#neutrons] [#nuclei] x [binding energy] [Fermi momentum] [nucleus target]
    
where only first three arguments are required.

!!! hint ""
    When using local Fermi gas model binding energy and Fermi momentum
    are taken from tables anyway.
    
You may add as many nuclei as you want in the following way:

    target_content += [#protons] [#neutrons] [#nuclei] x [binding energy] [Fermi momentum] [nucleus target]
    
!!! warning ""
    Please note `+=`. Using `=` would clear the previous content.

In general, a multi-nuclei target definition would look like this:

    target_content = [#protons 1] [#neutrons 1] [#nuclei 1] x
    target_content += [#protons 2] [#neutrons 2] [#nuclei 2] x
    target_content += [#protons 3] [#neutrons 3] [#nuclei 3] x


!!! note "Example"
     To define $C_2H_6O$ use the following code:
     
        target_type = 1
        target content = 6 6 2 x
        target content += 1 0 6 x
        target content += 8 8 1 x

### Predefined targets

One can also use predefined target specifications instead of the above parameters.
The list of targets can be found in `nuwro/data/target` directory.
To use one of those beams, one must use the following line:

```
@target/targetfile.txt
```

where `targetfile.txt` is the name of the file from `nuwro/data/target` directory.

!!! warning ""
    Please note that many predefined targets use slightly different syntax for composed targets. The syntax without space after `[#nuclei]` will not work on MacOS.

## Interactions settings

| Parameter name | Possible arguments | Default value | Description |
|:--------------:|:------------------:|:-------------:|:-----------:|
| dyn_qel_cc | 0, 1 | 1 | Turn on/off charge current quasielastic process |
| dyn_qel_nc | 0, 1 | 1 | Turn on/off neutral current quasielastic process |
| dyn_res_cc | 0, 1 | 1 | Turn on/off charge current resonance pion production |
| dyn_res_nc | 0, 1 | 1 | Turn on/off neutral current resonance pion production |
| dyn_dis_cc | 0, 1 | 1 | Turn on/off charge current deep inelastic scattering |
| dyn_dis_nc | 0, 1 | 1 | Turn on/off neutral current deep inelastic scattering |
| dyn_coh_cc | 0, 1 | 1 | Turn on/off charge current coherent pion production |
| dyn_coh_nc | 0, 1 | 1 | Turn on/off neutral current coherent pion production |
| dyn_mec_cc | 0, 1 | 1 | Turn on/off charge current meson exchange current process |
| dyn_mec_nc | 0, 1 | 1 | Turn on/off neutral current meson exchange current process|

### Quasi-elastic

| Parameter name | Possible arguments | Default value | Description |
|:--------------:|:------------------:|:-------------:|:-----------:|
| qel_vector_ff_set | 1-6 | 2 | Electromagnetic form factors parametrization:<br>1 - dipole form<br>2 - BBBA05 (Ref. [1])<br>3 - BBA03 (Ref. [2])<br>4 - JLab (Ref. [3])<br>5 - NN10 with two photon exchange effect (Ref. [4]) |
| qel_axial_ff_set | 1-4 | 1 | Axial form factors parametrization:<br>1 - dipole form<br>2 - 2-fold parabolic modification<br>3 - 3-fold parabolic modification<br>4 - 4-fold parabolic modification |
| qel_strange | 0, 1 | 0 | Turn on/off the strange quark contribution to the NC axial form factors |
| qel_strangeEM | 0, 1 | 0 | Turn on/off the strange quark contribution to the NC vector form factors |
| delta_s | any number | -0.15 | Strangeness contribution |
| qel_cc_axial_mass | positive number | 1200 | The axial mass value for charge current form factors |
| qel_nc_axial_mass | positive number | 1350 | The axial mass value for neutral current form factors |
| qel_s_axial_mass | positive number | 1200 | The axial mass value used in the dipole strange form factor |
| qel_rpa | 0-3 | 0 | RPA settings:<br>0 - do not use RPA<br>1 - use RPA without effective mass of nucleon<br>2 - use effective mass of nucleon without RPA (test only)<br> 3 - use RPA with effective mass of nucleon (test only)
| flux_correction | 0, 1 | 1 | Turn on/off flux correction |
| sf_method | 0-3 | 0 | Spectral function settings (for CCQE):<br>0 - do not use spectral function<br>1 - use grid spectral function (for 12C, 16O, 40Ar, 40Ca, 56Fe)<br>2 - use factorized spectral function (for 16O, 40Ar, 40Ca) |
| cc_smoothing | 0, 1 | 1 | If 1, the impossible quasi-elastic reaction (like CC nu scattering off proton) are skipped |

### Pion production

| Parameter name | Possible arguments | Default value | Description |
|:--------------:|:------------------:|:-------------:|:-----------:|
| delta_FF_set | 1-7 | 1 | Delta production form factors:<br>1 - dipole form [5]<br>2 - Paschos and Lalakulich, 2.12 MA = 1.05GeV BNL fit (Ref. [6])<br>3 - Paschos and Lalakulich, 2.12 MA = 0.84GeV ANL fit (Ref. [6])<br>4 - Paschos and Lalakulich, page 4, bottom right (Ref. [6])<br>5 - Paschos and Lalakulich, page 5, top left (Ref. [6])<br>6 - Eq. 13 from Ref. [7]<br>7 - based on chiral quark model from Ref. [8] |
| pion_axial_mass<br>*(for delta_FF_set 1)* | positive number | 0.94 | The axial mass value used in dipole parametrization of the resonance pion production form factor |
| pion_C5A<br>*(for delta_FF_set 1)* | positive number | 1.19 | The C5A value used in dipole parametrization of the resonance pion production form factor |
| spp_precision | positive number | 500 | Controls the precision in RESDIS boundary region; should not be changed |
| red_dis_cut | positive number | 1600 | Boundary of RES-DIS transition; should not be changed |
| coh_mass_correction | 0, 1 | 1 | Turn on/off Rein Sehgal correction to charge current coherent pion production |
| coh_new | 0, 1 | 1 | Change between old (0) and improved (1) implementation of coherent pion production |

### Two-body current

| Parameter name | Possible arguments | Default value | Description |
|:--------------:|:------------------:|:-------------:|:-----------:|
| mec_kind | 1-4 | 1 | Two-body current models:<br>1 - Transverse Enhancement model (Ref. [9])<br>2 - based on Marteau model (Ref. [10, 12])<br>3 - Nieves et al. model (Ref. [11]) |
| mec_ratio_pp | [0,1] | 0.6 | The fraction of mixed initial nucleon pairs for charge current interaction. For neutral current the fraction is calculated as 1/(2*mec ratio pp + 1) |

### Final state interactions

| Parameter name | Possible arguments | Default value | Description |
|:--------------:|:------------------:|:-------------:|:-----------:|
| kaskada_on | 0, 1 | 1 | Turn on (1) / off (0) final state interactions |
| kaskada_w | positive number | 7 | The value of the effective potential subtracted from the nucleons energy leaving the nucleus |
| kaskada_redo | 0, 1 | 0 | If on, given output file (*eventsout.root* by default) is loaded, the primary vertex is copied and only final state interactions are simulated. New output file with *.fsi.root* suffix is created |
| kaskada_writeall | 0, 1 | 0 | If on, all particles created during final state interactions are saved in *all* vector |
| step | positive number | 0.2 | Length of max step in the cascade in fm |
| xsec | 0, 1 | 1 | Cross section models for pion-nucleon interactions:<br> 0 - based on Ref. [13]<br>1 - based on Ref. [14] |
| pauli_blocking | 0, 1 | 1 | Turn on/off Pauli blocking |
| formation_length<br>*(for formation_zone const)* | positive number | 1 | Formation length in fm |
| tau | positive number | 8 | The parameter control the formation length for *ranft* and *rl* models |
| first_step | 0, 1 | 0 | If off, the formation zone is applied only for the particles created during final state interactions |
| formation_zone | *explained below* | fz | explained below |

### Formation zone models

* `nofz`: formation zone is off
* `skat8`: SKAT parametrization (Ref. [15])
* `cosyn`: parametrization based on Color Transparency measurements (Ref. [16])
* `cohl`: coherence length (Ref. [17])
* `ranft`: parametrization based on hadron-hadron and hadron-nucleus collision (Ref. [18])
* `rl`: as *ranft* but with fixed transverse momentum equal zero
* `delta`: for resonance pion production; based on ∆ lifetime (Ref. [19])
* `const`: constant value (defined by *formation_length*)
* `fz` (default):
    * `cohl` for quasi-elastic scattering
    * `delta` for resonance pion production
    * `ranft` for deep inelastic scattering
    * `nofz` for meson exchange current
* `trans`: only for nuclear transparency analysis

## References

[1] R. Bradford et al. “A New parameterization of the nucleon elastic form-factors”. Nucl.Phys.Proc.Suppl.
159 (2006), pp. 127–132.

[2] Howard Scott Budd, A. Bodek, and J. Arrington. “Modeling quasielastic form-factors for
electron and neutrino scattering” . arXiv: hep-ex/0308005 (2003).

[3] E.J. Brash et al. “New empirical fits to the proton electromagnetic form-factors”. Phys.Rev.
C65 (2002), p. 051001.

[4] Krzysztof M. Graczyk, Piotr Plonski, and Robert Sulej. “Neural Network Parameterizations
of Electromagnetic Nucleon Form Factors”. JHEP. 1009 (2010), p. 053.

[5] K.M. Graczyk, D. Kielczewska, P. Przewlocki, and J.T. Sobczyk. "C(5)**A axial form factor from bubble chamber experiments". Phys.Rev. D80 (2009) 093001

[6] Olga Lalakulich and Emmanuel A. Paschos. “Resonance production by neutrinos. I. J = 3/2
resonances”. Phys.Rev. D71 (2005), p. 074003.

[7] L. Alvarez-Ruso, S. K. Singh, and M. J. Vicente Vacas. “Charged current weak electroproduction
of the ∆ resonance”. Phys. Rev. C. 57 (5 1998), pp. 2693–2699.

[8] D. Barquilla-Cano, A.J. Buchmann, and E. Hernandez. “Axial N-¿Delta(1232) and N-¿N*(1440)
transition form factors”. Phys.Rev. C75 (2007), p. 065203.

[9] A. Bodek, H.S. Budd, and M.E. Christy. “Neutrino Quasielastic Scattering on Nuclear Targets:
Parametrizing Transverse Enhancement (Meson Exchange Currents)”. Eur.Phys.J. C71 (2011), p. 1726.

[10] Jan T. Sobczyk. “Modeling nuclear effects in neutrino interactions in 1-GeV region” . arXiv:
nucl-th/0307047 (2003).

[11] J. Nieves, I. Ruiz Simo, and M. J. Vicente Vacas. “Inclusive charged-current neutrino-nucleus
reactions”. Phys. Rev. C. 83 (4 2011), p. 045501.

[12] M. Martini et al. “Unified approach for nucleon knock-out and coherent and incoherent pion
production in neutrino interactions with nuclei”. Phys. Rev. C. 80 (6 2009), p. 065501.

[13] N. Metropolis et al. “Monte Carlo Calculations on Intranuclear Cascades. I. Low-Energy
Studies”. Phys.Rev. 110 (1958), pp. 185–203.

[14] L.L. Salcedo et al. “Computer simulation of inclusive pion nuclear reactions”. Nucl.Phys.
A484 (1988), p. 557.

[15] D.S. Baranov et al. “An estimate for the formation length of hadrons in neutrino interactions”
(1984).

[16] Wim Cosyn. “Exploring the limits of a hadronic picture of nuclei through pion and nucleon
removal reactions”. PhD thesis. Ghent University, 2009. url: http://lib.ugent.be/fulltxt/RUG01/001/350/817/RUG01-001350817/_2010/_0001/_AC.pdf1

[17] A. Rubbia G. Battistoni A. Ferrari and P.R. Sala. The FLUKA nuclear cascade model applied
to neutrino interactions. talk given at NuInt02. 2002.

[18] J. Ranft. “Hadron production in hadron-nucleus and nucleus-nucleus collisions in a dual
parton model modified by a formation zone intranuclear cascade”. Zeitschrift f¨ur Physik C
Particles and Fields. 43.3 (1989), pp. 439–446.

[19] Tomasz Golan, Cezary Juszczak, and Jan T. Sobczyk. “Final State Interactions Effects in
Neutrino-Nucleus Interactions”. Phys.Rev. C86 (2012), p. 015505.
