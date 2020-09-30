---
layout: page
title: "Gaussian guide"
permalink: /gaussian_guide/
---

Section|Content|
---|-----------------------|
1|[Preamble](#preamble)|
2|[Preparing Gaussian file](#preparing-gaussian-file)|
3|[Gaussian input](#gaussian-input)|
4|[Submitting job](#submitting-job)|
5|[Checking job status](#checking-job-status)|
6|[Viewing completed jobs](#viewing-completed-jobs)|
7|[Potential energy surface](#potential-energy-surface)|
8|[PES for dihedrals and 3D](#pes-for-dihedrals-and-3d)|
9|abc|
10|abc|
### Preamble
1. This guide will serve to help you do simple DFT calculations with the [Gaussian software](http://gaussian.com/), a popular tool for quantum chemical calculations with the NCI Gadi system.
2. To first access Gadi, you need to install your preferred remote access tool and for me personally I use [MobaXterm](https://mobaxterm.mobatek.net/) on Windows. I think it should be installable for Mac. MobaXterm is quite powerful as it can access GUI software like GaussView or VMD from Gadi using Xterm. If you are using Mac you can remotely access using the [terminal](https://www.booleanworld.com/access-mac-ssh-remote-login/). 
3. The next is to acquire a File Transfer Protocol (FTP) client for downloading and uploading files into or from Gadi. I use WinSCP but for Mac there is [Filezilla](https://filezilla-project.org/) or [Cyberduck](https://cyberduck.io/). In Mac you can also use the Terminal (similar to Linux environment) to transfer files between your computer and NCI’s Gadi, [see tips here](https://www.makeuseof.com/tag/mac-terminal-commands-cheat-sheet/). 
4. Typically, the commands to transfer to Gadi: ```scp yourfile userid@gadi.nci.org.au:/some/path/to/your/folder```. From Gadi to your computer: ```scp userid@gadi.nci.org.au:/some/path/to/your/folder/yourfile```
5. For more information on Gadi and useful basic commands on Linux: 
    * [Getting started with NCI Gadi](https://opus.nci.org.au/display/Help/Raijin+User+Guide)
    * [Linux basic guide from NCI](https://opus.nci.org.au/display/Help/Linux+Command+Quick+Reference+Guide)
    * [Linux basic guide from NSCC](https://help.nscc.sg/wp-content/uploads/2016/03/BasicLinuxTutorial-v0.1.pdf)
    * [Linux basic guide for Mac](https://www.makeuseof.com/tag/mac-terminal-commands-cheat-sheet/)

### Preparing Gaussian file
1. You can use any molecular GUI available like GaussView (GV), [Iqmol](http://iqmol.org/), Spartan or even [Avogadro](https://avogadro.cc/) to construct your structure. Here I made propane with GV. 
![propane_gv](/files/guide/propane_gv.png)
2. Iqmol (IQ) is a free software and you can download from [here](http://iqmol.org/). It is available on both Windows or Mac. I made a benzene ring here. It is highly recommended that you read the guide for these programs too. 
![propane_iq](/files/guide/propane_iq.png)

3. Once done, save the file into format that can be read by GV, i.e. .xyz or .pdb formats if you are using IQ.
![pdb_iq](/files/guide/pdb_iq.png)

4. For.pdb files you will need to open using GV, export and save it as the gaussian input file format .gjf. To do a proper conversion from .pdb to .gjf it is a little complicated. To do so, once you’ve loaded bezene.pdb in GV, go to >Calculate>Gaussian Calculation Setup… 

![pdb_gv](/files/guide/pdb_gv.png)

5. In the Setup panel click on ‘General’ tab:

![pdb_setup](/files/guide/pdb_setup.png)

6. Untick the ‘Write Connectivity’ and ‘Write PBD Data’ options but keep the ‘Write Cartesians’. Then click on ‘Submit’, and save the file as .gjf. If it prompts you to Submit the job into Gaussian click ‘No’.

### Gaussian input
1. Before sending the job to run in Gadi, you need to edit the benzene.gjf file with [Notepad++](https://notepad-plus-plus.org/). This editor is also useful for coding. You can use any editor tools you prefer. 
![raw_gjf](/files/guide/raw_gjf.png)

2. You can see the xyz cartesian coordinates of the atoms above. Another way of organising the atoms’ 3D information is the z-matrix format which we use to do PES scans.
3. Here are the edits you need to do on your raw gjf file in notepad++:
    *	Rename the ```%chk``` to benzene.chk (this is checkpoint file)
    *	Add ```%mem=8GB``` (To request 8GB memory in a node. This is different from the image.)
    *	Add ```%nprocshared=4``` (to request 4 cpus in a node. This is different from the image.)
    *	Add the optimization for minimum structure optimization: 
    ```# b3lyp/6-31G* opt=(maxcyc=200) scf=(maxcyc=200) freq```
    *	If you are calculating a transition state: 
    ```# b3lyp/6-31G* opt=(ts,calcfc,noeigen,maxcyc=200) scf=(maxcyc=200) freq```
    *	If you’re doing potential energy surface (PES) the atoms need to be in z-matrix format
    *	Insert comments or the job title. **Important: note the one black space after the keywords and one blank space after**
    *	0 1 – refers to 0 as the charge (this is a neutral structure) and 1 as the spin multiplicity 
    *	After the xyz leave 2 blank lines - **this is important!**
![finish_gjf](/files/guide/finish_gjf.png)

### Submitting job
1. If this is your first time setting up in Gadi, you will need to setup your own folder:
    * Change directory (CD) to zl12 project folder: ```cd /g/data/zl12/```
    * Make your own directory: ```mkdir <user_id>```
    * CD to your directory: ```cd <nci_id>```
    * Make your own project folder: ```mkdir <project_name>```
    * Your calculations will be placed in this project folder and whenever you run jobs wish to view them jobs you can CD: ```cd /g/data/zl12/<user_id>/<project_name>```
    * **Note: your project name or files must never have spaces or special chars between them like 'proj 1' or 'proj&1'. You should instead name them as 'proj_1' or simply 'proj1'**
2. To edit a new file in the bash you can either use ```nano``` or ```vi```. Personally, I prefer ```nano```:
![nano_empty](/files/guide/nano_empty.png)

3. Copy and paste over the lines in benzne.gjf you saved in your editor to MobaXterm (just do a right click). Save and exit by ‘Ctrl-X’.
![nano_filled](/files/guide/nano_filled.png)

4. Use the script ```_g16``` to run the benzene.gjf job. Or you can simply make one with ```nano```:
```
#!/bin/bash
## To submit, ./_g16 <filename> <time> <cpu> <mem> 
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
5. **It is important to ensure that the ```_g16``` file is executable by doing ```chmod +x _g16```.**
6. You only need to do this once and executable files are green in MobaXterm. 
7. To run the benzene.gjf job, you need to dispatch the job to a PBS Pro scheduler or normally what we do is to ```qsub``` but I’ve already added this command into the ```_g16``` script so not necessary to ```qsub```. You can look at the script’s code if you’re interested. I’ve set all the parameters to run the job using resources for a node but you can change them as you like for default.
8. Finally to submit the job type in the directory where your file is: ```./_g16 benzene```. You’ll need to include the filename and time which I put as default of 24 hours. 
9. The job-hours depend on what kind of jobs you are running. Obviously, PES will usually be longer say 48 hours and you can submit by ```./_g16 <file_name> 48``` (which is the maximum run-time in Gadi). In this example, the benzene calculation takes only a few seconds! Note that the PBS will return a 4213261 which is the job ID. And if successful there will be an echo out of the job status. 
![job_submit](/files/guide/job_submit.png)

## Checking job status
1. You can check all jobs running by ```qstat -u <userid>```:
![qstat](/files/guide/qstat.png)

2. Or you can directly ```qstat``` the jobid by ```qstat -f ```.
3. To stop the job you can ```qdel <job_id>```.
4. Once the job is completed, there will be no more lines when you ```qstat```. When the job is being run, a benzene.log file is being created. If you ```tail benzene.log``` a completed log file you will see some quotes and a ‘Normal termination of Gaussian 16 t …’ line:
![normal_termination](/files/guide/normal_termination.png)

5. Transfer this file into your own computer harddisk using the FTP client.

## Viewing completed jobs
1. You can view the benzene.log with GV in the MobaXterm by typing ```gview benzene.log``` or alternatively if you have GV installed in your computer you can view it from there. But this is the view of the completed structure. Near the green button you see 3 of 3 which meant that this job took 3 steps to complete because our guess structure is very close to the electronically optimized one at B3LYP level of theory.
![benzene_calc_done](/files/guide/benzene_calc_done.png)

2. Click on the panel with the ‘Results’ tab to see the summary of the calculations. 
![calc_summary](/files/guide/calc_summary.png)

3. Under the ‘Thermo’ tab, there will be some values we are interested in, i.e. the ‘Electronic Energy’, and ‘Thermal correction to Free Energy’. 
![calc_summary_panel](/files/guide/calc_summary_panel.png)

4. To find the free energy of this structure, we add the EE + Thermal Free Energy Correction (fourth line from the last). If you’re looking at the enthalpy, then is EE + Thermal Enthalpy Correction. Note that all this are taken at 298.15 K. Since we know that G = H – TS, the entropy is just (H – G)/T. 
5. More one final important thing to check is that the vibration frequencies (Results>Vibrations...) do not have imaginary values, i.e. all the values are positive for a minimum structure. Transition states have a value which is negative. Usually imaginary frequencies will show up as negative in Mode #1.

## Potential energy surface
1. Here I will show you the steps in running a PES scan to locate transition state (TS) structure and the corresponding minimum.
2. First I open an completed or optimized .log file in GV. This is a minimum structure of a Michael addition reaction and I am using the structure after the C-C bond is being formed, ie the Michael adduct. For the structure below we are going extend the two highlighted carbon atoms labelled 98 and 129 (select Label under View):
![michael_cat](/files/guide/michael_cat.png)    

3. Save the file as ‘PES-xxx.gjf’ and in the save option, remember to uncheck the ‘Write Cartesian’:
![PESfile_save](/files/guide/PESfile_save.png)

4. Load the ‘PES-xxx.gjf’ in notepad++:
![PES_notepad](/files/guide/PES_notepad.png)

5. You will find that the atom format is quite unlike the one previously used in calculating the benzene minimum structure which was in cartesian or xyz. The format here is z-matrix or z-mat and will be used primarily for PES. To format a PES .gjf file, there are several important edits required in the route as well as after the z-mat:
![PES_notepad_zoom1](/files/guide/PES_notepad_zoom1.png)

6. And at the end of the z-mat is the modredundant parameters:
![PES_notepad_zoom2](/files/guide/PES_notepad_zoom2.png)

7. Here are some of the important key-points:
    * Check that the name of the ```%chk``` is correct. And there are no spaces or special chars.
    * Add the option ‘modredundant’ and change to ```maxcyc=20``` in the ```opt``` or optimization keyword. There is no need for ```freq``` – it is  only required for optimization jobs.
    * Edit the title to indicate that this is a PES job.
    * After the tail end of z-mat add one blank space and key in the desired bond/angle/dihedral to manipulate. In this tutorial, the modredundant command ```B 129 98 S 60 0.024``` translates to a relaxed scan of the bond between atoms 129 and 98, scanning 60 steps and each step the distance to lengthened by 0.024 Å. Conversely, ```B 129 98 S 60 -0.024``` would result in the atoms 129 & 98 being brought closer.
    * How do I determine how many steps to run? This is really based on guestimation, intuition and experience. But usually for TS structure dealing with C-C bond addition, I would expect the distance to be about 2.5 Å based on my experience. As the minimum C-C bond is about 1.4 Å, I would expect after 41 steps, it would have reached 2.5 Å and the TS structure. The step size of 0.024 will usually give us a nice PES profile (see section on PES scan example). However, sometimes I will run step size of even 0.05 to get a rough estimate to cut down on computational time and cost. The con is that the PES profile might not give a clear sign of TS. 
    * The order of the atoms is important - 129 comes first as I want atom 129 and its connecting group of atoms to translate rather than atom 98 and its connecting group of atoms. You will see this once the job is run.
    * Finally, it is very important to leave a blank space or a few bank spaces after the command.

## PES for dihedrals and 3D

