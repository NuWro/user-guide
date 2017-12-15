[Singularity](http://singularity.lbl.gov/) containers are provided for all NuWro releases (starting from 17.09).

The builds can be found in [Singularity Hub](https://singularity-hub.org/collections/265).

Each container includes base OS, [ROOT](https://root.cern.ch/) with [PYTHIA6](https://pythia6.hepforge.org/) and NuWro (in `/opt/` folder).

For more information about Singularity please visit [Singularity Used Guide](http://singularity.lbl.gov/user-guide).

## Getting a container

One can easily get a Singularity container using `singularity pull`:

```bash
$ singularity pull shub://NuWro/builds:[release tag]
```

where `tag = YY.MM` (please make sure capitalization is correct - it is case sensitive), e.g. 

```bash
$ singularity pull shub://NuWro/builds:17.09
```

Downloaded image (by default) has the following name:

```bash
[user]-[repo]-[branch]-[tag].simg
```

e.g.

```bash
NuWro-builds-master-17.09.simg
```

Information about software versions installed in a container is stored in its metadata. To check it use `inspect`, e.g.

```bash
$ singularity inspect NuWro-builds-master-17.09.simg 
{
    "MAINTAINER": "tomasz.golan@gmail.com",
    "NUWRO": "17.09",
    "ROOT": "5.34/36",
    "OS": "Ubuntu14.04",
    ...
}

```

## Using a container

Any application can be executed from a container using `exec`, e.g. to run NuWro with default parameters

```bash
$ singularity exec NuWro-builds-master-17.09.simg nuwro
```

Arguments can be passed as usual, e.g.

```bash
$ singularity exec NuWro-builds-master-17.09.simg nuwro -i my_params.txt -o my_output.root
```

In the same way one can run `myroot` to analyse the output, e.g.

```bash
$ singularity exec NuWro-builds-master-17.09.simg myroot 
```

Also, NuWro containers are set up to run NuWro, by default:

```
singularity run [img file]
```

or 

```
./[img file]
```

are equivalent to `singularity exec [img file] nuwro`, e.g.

```
./NuWro-builds-master-17.09.simg -p "beam_energy = 1234"
```

would run NuWro with all deafult parameters but neutrino energy (equal 1234 MeV).

## Using ROOT5 container

One can also use the container with preinstalled ROOT 5 and PYTHIA6 to compile and use NuWro from local drive. To get the container:

```bash
$ singularity pull shub://NuWro/builds:root5
```

Start interactive shell:

```bash
$ singularity shell NuWro-builds-master-root5.simg
```

Go into NuWro folder (must be located somewhere in $HOME)

```bash
cd /home/user/my/path/to/nuwro
make
./bin/nuwro
```
