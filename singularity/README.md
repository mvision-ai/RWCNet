# Running with Singularity

## Run

Download the singularity image file:

```
wget https://utoronto-my.sharepoint.com/:u:/g/personal/m_hardisty_utoronto_ca/EbEFUPAVxn1KnSwk8wzvfi8Bb1fvGKE4nxDmufSJYQ2hmg?download=1 -O Some-RNN3.sif
```



```
singularity shell --nv --cleanenv Some-RNN3.sif
```

This will spawn a singularity instance and will present the singularity shell type the follow commands to start training

```
bash
conda init bash
bash
conda activate Some-RNN
cd /optimization-based-registration
python util.py convert-nlst-json /path/to/dataset/json /path/to/dataset /input/json/path
python run_rnn.py train /input/json/path /output/directory/path
```

Here is a complete example, note that here the singularity write results to a host directory.  This is the most convienient method for insepcting results files:
```
bash
conda init bash
bash
conda activate Some-RNN
cd /optimization-based-registration
python util.py convert-nlst-json ~/datasets/NLST-resampled/NLST_dataset.json  ~/datasets/NLST-resampled   ~/src/optimization-based-registration/singularityTraining/NLST-resampled-input.json
python run_rnn.py train ~/src/optimization-based-registration/singularityTraining/NLST-resampled-input.json ~/src/optimization-based-registration/singularityTraining
```

To use Singularity on Compute Canada cluster (Beluga, Cedar, Graham, Niagara) using CUDA follow the following instructions in shell. 
on shell submit job with a script as shown below:
```
$ sbatch train-job.sh 
```

where train-job.sh is a bash script shown below:

```
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --gpus-per-node=v100:1
#SBATCH --cpus-per-task=3
#SBATCH --mem=12G
#SBATCH --time=4-00:00
module load arch/avx512 StdEnv/2018.3
nvidia-smi
module load singularity
srun singularity exec --nv --cleanenv Some-RNN3.sif ~/my_singularity_script.sh

```
my_singularity_script.sh should look like this 

```
#!/bin/bash
source /opt/conda/bin/activate
conda activate Some-RNN
cd /optimization-based-registration
python util.py convert-nlst-json ~/datasets/NLST-resampled/NLST_dataset.json  ~/datasets/NLST-resampled   ~/src/optimization-based-registration/singularityTraining/NLST-resampled-input.json
python run_rnn.py train ~/src/optimization-based-registration/singularityTraining/NLST-resampled-input.json ~/src/optimization-based-registration/singularityTraining
```


