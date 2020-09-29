---
layout: page
title: "Gaussian guide"
permalink: /gaussian_guide/
---

## Table of contents

Content| |
---|---|
1|[Preamble](#preamble)|
2|[Preparing Gaussian file](#preparing-gaussian-file)|

[linux basic guide](https://opus.nci.org.au/display/Help/Linux+Command+Quick+Reference+Guide)

### Preamble

This is script to run in Gadi:

```
#!/bin/bash
## To submit, ./_g16 <filename> <time> <cpu> <mem> or put g16r2 in ~/bin/ folder
## (with or without .gjf extension; time in hrs otherwise default is 24 hrs;
## cpu default is 4; memory default is 8GB)
## Edited 20/4/2020 ~Richmond
## Input variables:
INPUT=`basename $1 .gjf`
TIME=${2:-24}
CPU=${3:-4}
MEM=${4:-8GB}
JOBST=128GB
PROJ=zl12

## check if job exists
if [ ! -e $INPUT.gjf ]; then
   echo "   -> !!!!! [$INPUT.gjf] does not exist !!!!! "
   exit -1
fi

cat << EOF > $PBS_O_WORKDIR\g16.sh
#!/bin/bash
#PBS -l walltime=$TIME:00:00
#PBS -l ncpus=$CPU
#PBS -l mem=$MEM
#PBS -l jobfs=$JOBST
#PBS -l storage=gdata/zl12
#PBS -l wd
#PBS -e $INPUT.e
#PBS -o $INPUT.o
#PBS -N $INPUT
#PBS -P $PROJ

echo "Started job on `date` "

cd \$PBS_O_WORKDIR

cpulist=`grep Cpus_allowed_list: /proc/self/status | awk '{print $2}'`
echo "%CPU=$cpulist" > \$PBS_JOBFS/\$INPUT.gjf
cat \$INPUT.gjf >> \$PBS_JOBFS/\$INPUT.gjf

module load gaussian/g16a03
g16 -P=$CPU <$INPUT.gjf> $INPUT.log 2>&1

EOF

qsub g16.sh

echo "   -> Job [$INPUT.gjf] submitted! $CPU cpus; $MEM mem; running $TIME hrs"
```

### Preparing Gaussian file
You can use any molecular GUI available like GaussView (GV), [Iqmol](http://iqmol.org/), Spartan or even [Avogadro](https://avogadro.cc/) to construct your structure. Here I made propane with GV.
![propane](/files/guide/propane.png)
