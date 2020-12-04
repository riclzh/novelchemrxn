---
layout: page
title: "XTB guide"
permalink: /xtb_guide/
---

Section|Content|
---|---|
1|[Preparation](#prep)|
2|[XTB](#xtb)|
3|[Running](#running)|

### Prep
1. This guide will serve to help you do [XTB](https://xtb-docs.readthedocs.io/en/latest/gfnff.html) calculations linked to [Gaussian software](http://gaussian.com/).
2. A script (xtb.sh) and two executables (genxyz and extderi) must be downloaded from [gau_xtp](http://sobereva.com/soft/gau_xtb/). The code is written by Dr [Tian Lu](http://sobereva.com/Tian_Lu.html) with many other interesting programs. 
3. Download the precompiled version of [XTB](https://github.com/grimme-lab/xtb/releases/tag/v6.3.3) and transfer the compressed file into Gadi.
4. After ```tar xf xtb-200917.tar.xz``` to extract the tarball you will find that it should contain ```bin``` and ````share```.
5. In home ```/~``` direction ```nano .bashrc``` and add these lines:
```
export PATH=$PATH:~/xtb-6.3.3/bin
export XTBPATH=~/xtb-6.3.3/share/xtb
export OMP_NUM_THREADS=4
export MKL_NUM_THREADS=4
export OMP_STACKSIZE=1000m
ulimit -s unlimited
```
6. Exit and ```. .bashrc```

### XTB
1. The calculations will use the parameter which is GFN2-xtb. This is a semi-empirical tight binding model which means it will take into account non-covalent interactions and is up a level from semi-empirical methods like PM6. 
2. Read more about the method [here](https://pubs.acs.org/doi/10.1021/acs.jctc.8b01176)
3. Since GFN2 is considered a semi-empirical method, it should be applicable to calculating large molecules like small proteins.
4. The computational cost is lower than DFT methods like B3LYP. 
5. You can consider using this method to calculate and locate minimum of transition state structures first. It is linked by gau_xtb program with Gaussian.

### Running


