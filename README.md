# Long Context in Small LMs with SelfExtend

Course project for [CSE 60556] Large Language Models

## Updates

- \[Oct 28\]: Add `requirements.txt` for easier Python environment construction.
- \[Oct 28\]: Repreduced Gemma-2b (without SelfExtend) on LongBench, it needs at least 3 A10s to run.
- \[Oct 20\]: Created README.
- \[Oct 19\]: Reproduced Phi-2 with SelfExtend on LongBench.
- \[Oct 17\]: Reproduced Llama2-7b and Phi-2 on SelfExtend `example.py`.

## Setup

1. Clone this repository on CRC
2. Download [this docker image](https://hub.docker.com/r/hoytjin/selfextend_docker/tags) somewhere on CRC
3. Make sure you have enough storage on your CRC account, I have emailed crcsupport@nd.edu to open a 500 GB storage space in `/scratch365/`

## Usage

`LongBench` and `LongLM` have to be in the same directory to apply SelfExtend.

I have added Python script arguments in `LongBench/pred.py` and `LongBench/eval.py` to accept `--se`. Including this option will apply SelfExtend to LongBench scripts.

### GPU usage

I have not yet tested how many GPU cards are suitable for each task, but generally **2 A10 GPU cards** should suffice, even for Llama2-7b.

## Job Scripts

To run `LongLM/example.py`, use the following job script:
```
#!/bin/bash

#$ -M your_netid@nd.edu
#$ -m ae
#$ -pe smp 24
#$ -q gpu@@crc_gpu
#$ -l gpu_card=1
#$ -N longlm-example
#$ -o longlm-example.out

echo 'This script is run with 1 gpus'

APPTAINER_PATH=${PATH}
APPTAINER_LD_LIBRARY_PATH=${LD_LIBRARY_PATH}

module load cuda/11.8 # Downloaded Docker environment requires cuda 11.8, CRC's default is cuda 12.1

apptainer exec \
	-B /afs -B /opt/crc -B /scratch365 \ # mount all root dirs to docker
	--no-home --nv \
	/path/to/your/docker/file.sif \
	bash -c \
	"cd /path/to/your/long-context-small-lm/LongLM/ && python example.py"

echo 'Script completed'
```

To run `LongBench/pred.py` (using Phi-2 as an example), use the following job script:
```

#!/bin/bash

#$ -M your_netidy@nd.edu
#$ -m ae
#$ -pe smp 24
#$ -q gpu@@crc_gpu
#$ -l gpu_card=2
#$ -N longbench-phi-2
#$ -o longbench-phi-2.out

echo 'This script is run with 2 gpus'

APPTAINER_PATH=${PATH}
APPTAINER_LD_LIBRARY_PATH=${LD_LIBRARY_PATH}

module load cuda/11.8

export APPTAINERENV_CUDA_VISIBLE_DEVICES=${CUDA_VISIBLE_DEVICES}
apptainer exec \
	-B /afs -B /opt/crc -B /scratch365 \
	--no-home --nv \
	/path/to/your/docker/file.sif \
	bash -c \
	"cd /path/to/your/long-context-small-lm/LongBench/ && python pred.py --model phi-2"

echo 'Script completed'

```

## TODOs

- [ ] Reproduce Gemma (NOTE: might need to change precision when loading the model)
- [ ] Add more model configs to `LongBench/config/` for it to be compatible with other models
- [ ] More ...
