## A short introduction to use anaconda (and your customized python environment) on Sango (or other similar clusters).

As for the basic knowledge of how to use Sango, please refer to [getting-started](https://groups.oist.jp/scs/getting-started)

The author is not sure if this is the best way to run python programs with customized environment, but just provides the method he used for running his programs.

Let's start step-by-step!

### Allocate a bash for your installing.
Some installing need much CPU power, so it is better to allocate some source first.
```bash
srun -p compute -t 10:00:00 --pty bash -l
```

### Installing :
After you allocate a bash, you can go to your user directory (on Sango it will be /home/d/dongqihan or similar, where d is the first letter of username).
```bash
cd ~
```
Then download an anaconda from web and install it. For example:

```bash
curl -O https://repo.continuum.io/archive/Anaconda3-5.0.1-Linux-x86_64.sh
bash Anaconda3-5.0.1-Linux-x86_64.sh
```

### Using anaconda to create your own virtual environment
After installing anaconda, you should know where it is installed. Then you can export the PATH of it bin folder. For example, 
```bash
export PATH=/home/d/dongqihan/conda/anaconda/bin:$PATH
```
Then you can start using it, just like on your own computer. Now you can create a new environment:
```bash
conda create -n CusEnv python=3.6
source activate CusEnv
```

### Installing necessary packages
After activating the virtual environment, you can start installing python libraries. Note that many libraries require modules such as cmake, openmpi, cuda... So before installing them, just load the module first.
```bash
module avail
module load xxx yyy
```
Then you can install use pip or conda

```bash
pip install zzz
conda install kkk
```


### Using your own environment
With all required libraries installed, you may want to run your programs. One approach is to load the necessary modules and export the PATH and activate the environment in a bash shell file, *run.sh* , where inside is, for example

```bash
module load xxx yyy
export PATH=/home/d/dongqihan/conda/anaconda/bin:$PATH
source activate CusEnv
python deep_learning.py
```

Then you can srun *run.sh* simply by
```bash
srun -t 10:00:00 -n 1 -c 8 bash ./run.sh
```

or, use a slurm script, *job.slurm* :

```bash
#!/bin/bash

#SBATCH --job-name=python
#SBATCH --partition=compute
#SBATCH --time=1-0
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=2g
#SBATCH --output=/dev/null
#SBATCH --error=./errors/slurm-%A.err

# Display all variables set by slurm
env | grep "^SLURM" | sort

# Print hostname job executed on.
echo
srun bash ./run.sh
echo

```

and just submit it by 
```bash
sbatch job.slurm
```


## Made by
Dongqi Han, CNRU, OIST
<dongqi.han@oist.jp>
