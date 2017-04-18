## Getting NuWro

To get the most up to date version of NuWro use:

```bash
git clone https://github.com/NuWro/nuwro.git
```

To get one of the stable releases use:

```bash
git clone -b [release tag] https://github.com/NuWro/nuwro.git
```

e.g.

```bash
git clone -b nuwro_17.01 https://github.com/NuWro/nuwro.git
```

!!! fail "git version older than 1.7.10"
    `git clone -b [release tag]` will not work for git older than 1.7.10
    (but it is from May 2012 so you may consider updating your system).

    To get specific release you can use `git checkout tags/[release tag]`, e.g.

        git clone https://github.com/NuWro/nuwro.git
        git checkout tags/nuwro_17.01

## Dependencies

* [ROOT](https://root.cern.ch/)
* [PYTHIA6](https://pythia6.hepforge.org/)

### Building ROOT with PYTHIA6 support

* download [ROOT source code](http://root.cern.ch/drupal/content/downloading-root)
* make sure you have all [ROOT dependencies](http://root.cern.ch/drupal/content/build-prerequisites) installed
* download [PYTHIA6 source code](http://neutrino.ift.uni.wroc.pl/files/pythia6.tar.gz)
* build PYTHIA6 shared object (`libPythia6.so`):

```bash
tar -xzvf pythia6.tar.gz
cd pythia6 && ./makePythia6.linux
```

!!! important ""
    It requires g77 Fortran compiler (or g77 linked to gfortran).

* copy `libPythia6.so` to `root/lib` folder:

```bash
tar -zxvf root_v*.source.tar.gz
mkdir root/lib
cp pythia6/libPythia6.so root/lib
```

* configure ROOT:

```bash
cd root && ./configure --with-pythia6-libdir=lib
```

* and make it:

```bash
make
```

* set environmental variables: create a script, like `setup_root.sh`

```bash
export ROOTSYS=(path to root directory)
export PATH=$PATH:$ROOTSYS/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ROOTSYS/lib
```

and run `source setup_root.sh` every time you start a terminal
or add the above lines to either `~/.bashrc` or `~/.bash_profile`.

## Building NuWro

Once you have ROOT installed with PYTHIA6 support just make NuWro:

```bash
tar -zxvf nuwro-*.tar.gz
cd nuwro && make
```

## Troubleshooting

* If you have troubles to compile ROOT with gcc5 compiler
and getting errors about undefined references to *std::__cxx11*
(described [here](https://gcc.gnu.org/onlinedocs/gcc-5.2.0/libstdc++/manual/manual/using_dual_abi.html)),
you can add the following flag:

```bash
-D_GLIBCXX_USE_CXX11_ABI=0
```

to *CFLAGS* in proper *[root]/config/Makefile.[system]* file.
