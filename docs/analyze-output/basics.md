!!! attention ""
    Work in progress. Meanwhile, you can find more information in the
    [tutorial](http://www.ift.uni.wroc.pl/%7Etgolan/talks/NuWro_howto.pdf).

## Event

Generated events are represented by objects of `class event : public TObject`.
They are saved in an output *ROOT* file under a *TTree* named **"treeout"**.
Each `event` object stores all information about a single neutrino scattering process.

This section describes informations you can extract from an `event` object.

!!! note
    `class event : public TObject` provides many useful public variables and methods
    to use when analyze a ROOT output file.
    This section describes some of them. For the full list please refer to
    `nuwro/src/event1.h`.

### Flags

`event.flag` stores the structure of the following flags:

| Flag | True for |
|:-----|:---------|
| `qel`| (quasi-)elastic event                              |
| `res`| pion production event through resonance excitation |
| `dis`| deep inelastic scattering event                    |
| `mec`| two-body current event                             |
| `nc` | neutral current event                              |
| `cc` | charged current event                              |

!!! warning ""
    Please note that RES channel is mainly pion production through
    Delta resonance, however, in general it is defined as inelastic
    events with invariant mass below 1.6 GeV. Everything above that
    value is marked as DIS channel.

### Vectors of particles

There are three main `std::vectors` of particles:

| Vector | Stores |
|:-------|:-------|
| `in`   | initial state particles in primary vertex            |
| `out`  | final state particles in primary vertex              |
| `post` | final state particles after final state interactions |

!!! note
    The convention is that a lepton is always first particle in a vector, so:
    
    * `in[0]` - incoming neutrino
    * `out[0]` - outgoing neutrino / charged lepton

More information about `particle` class can be found in [particle section](#particle).

#### Particles multiplicities 

Although one can grab particles multiplicities directly from vectors,
there are several methods to make it easier:

| Method | Returns the number of final state particles |
|:-------|:--------------------------------------------|
| `int n();`          | after primary vertex                                 |
| `int f();`          | after final state interactions                       |
| `int nof(int pdg);` | after primary vertex given by the PDG code           |
| `int fof(int pdg);` | after final state interactions given by the PDG code |

### Kinematics

There are only few kinematics methods for an event
(see [particle section](#particle) for more):

| Method | Returns |
|:-------|:--------|
| `vect q();`    | four-momentum transfer (more on [vect class](#vectors)) |
| `double q2();` | four-momentum transfer squared |
| `double W`();` | invariant mass |

!!! note ""
    Please note default NuWro units is MeV.

### Final state interactions

The following methods return the number of specific interaction type,
happened during final state interactions:

| Method | Returns the number of |
|:-------|:----------------------|
| `int number_of_nucleon_elastic();` | nucleon elastic                |
| `int number_of_nucleon_spp();`     | nucleon single pion production |
| `int number_of_nucleon_dpp();`     | nucleon double pion production |
| `int number_of_pion_elastic();`    | pion elastic                   |
| `int number_of_pion_ce();`         | pion charge exchange           |
| `int number_of_pion_spp();`        | pion single pion production    |
| `int number_of_pion_dpp();`        | pion double pion production    |
| `int number_of_pion_tpp();`        | pion triple pion production    |
| `int number_of_pion_abs();`        | pion absorption                |
| `int number_of_interactions();`    | the sum of above               |


### Simulation settings

`event.par` stores the copy of parameters used for a simulation.
Please note, that some values may be different from event to event
(e.g. target nucleus for detector simulations).

## Particle

Events keep information about initial and final states particles
in `std::vector <particle>` (as described [here](#vectors-of-particles)).
Usually, one has to loop over these vectors to extract information about
final state particles.

This section describes informations you can find in a `particle` class object.

!!! note
    `class particle : public vect` provides many useful public variables and methods
    to use when analyze a ROOT output file.
    This section describes some of them. For the full list please refer to
    `nuwro/src/particle.h`.
    
### Selected methods

| Method | Returns |
|:-------|:--------|
| `double E();`         | total energy           |
| `double energy();`    | total energy           |
| `double Ek();`        | kinetic energy         |
| `double m();`         | mass                   |
| `double mass();`      | mass                   |
| `double mass2();`     | mass squared           |
| `double momentum();`  | value of the momentum  | 
| `double momentum2();` | momentum squared       |
| `vec p();`            | momentum as a 3-vector |
| `vect& p4();`         | four-momentum          |
| `bool lepton();`      | true if lepton         |
| `bool pion();`		| true if pion           |
| `bool nucleon();`		| true if nucleon        |

!!! note ""
    Please note default NuWro units is MeV.

### Selected variables

| Method | Description |
|:-------|:------------|
| `int pdg;` | PDG code |


## Vectors

There are two classes for handling vectors:

* `class vec`: 3-vectors (x, y, z)
* `class vect`: 4-vectors (t, x, y, z)

They both overload all reasonable operators, so vectors algebra is straightforward.

Not much to say about it - they are vectors. If you feel like reading more on that
please see `nuwro/src/vec.h` and `nuwro/src/vect.h`.


