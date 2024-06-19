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
    It requires `gfortran` Fortran compiler.

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
For ROOT version 6.16.00 you can try out the following [script](http://neutrino.ift.uni.wroc.pl/files/download-and-make-root6.16.00-with-pythia6.sh) :
```bash
#make temporary directory for downloading sources
mkdir ~/install
cd ~/install
#dowload pythia6 sources 
wget https://root.cern.ch/download/pythia6.tar.gz
tar xzf pythia6.tar.gz
cd pythia6
#modify ./makePythia6.linux to use gfortran in place of g77
sed -i 's/g77/gfortran/g' ./makePythia6.linux
#make libPythia6.so
bash ./makePythia6.linux
#download ROOT sources
cd ~/install
wget https://root.cern/download/root_v6.16.00.source.tar.gz
tar zxf root_v6.16.00.source.tar.gz
#create build directory
mkdir ~/root
cd ~/root
mkdir ~/root/lib
#copy pythia6 library to build directory
cp ~/install/pythia6/libPythia6.so  ~/root/lib
#cmake ROOT with PYTHIA6 enabled
cmake -DPYTHIA6_LIBRARY=~/root/lib/libPythia6.so -Dpyhia6=ON ~/install/root-6.16.00
#make it
make -j4
```

* set environmental variables:

```bash
export ROOTSYS=(path to root directory)
export PATH=$PATH:$ROOTSYS/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ROOTSYS/lib
```

you can use the script provided by ROOT (`bin/this_root.sh`) that includes the commands and run `source bin/this_root.sh` every time you start a terminal or add the above lines to either `~/.bashrc` or `~/.bash_profile`.

!!! note "macOS support"
    NuWro has been tested to work properly on the following configurations

        x86_64-apple-darwin15.6.0
        clang-800.0.42.1
        root-5.34.36

        arm64-apple-darwin23.2.0
        clang-1500.1.0.2.5
        root-6.30.04

    While installing on macOS one should remember about the following differences:

    * PYTHIA6 should be compiled using the `./makePythia6.macosx64` command,
    * the shared object of PYTHIA6 is called `libPythia6.dylib`,
    * the bash profile file is called `~/.profile` or `~/.zshrc`.

    Note that the following problems are known to emerge:

    * while installing `gfortran` via Homebrew the `libgfortran.dylib` file is not properly linked for the C compiler, one should export the variable `LIBRARY_PATH` with the path to the lib file.<br>
    e.g. put into `~/.profile` or `~/.zshrc` the following line (it will depend on the `gfortran` installation)

        `export LIBRARY_PATH=/usr/local/lib/gcc/6`




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
