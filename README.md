# Long Context in Small LMs with SelfExtend

Course project for [CSE 60556] Large Language Models

## Updates

- \[Nov 11\]: Update README.
- \[Nov 11\]: Phi models are run successfully, Gemma models have "8193 error", **Llama3.2 models need more work**.
- \[Nov 10\]: Updates config files: change to more meaningful and consistent names.
- \[Nov 10\]: Updates requirements.txt, note that everything related to `torch` (including `flash_attn`) needs to be installed at the end
	- DO NOT USE DOCKER ENVIRONMENT, use `conda` instead
- \[Oct 28\]: Add `requirements.txt` for easier Python environment construction.
~- \[Oct 28\]: Repreduced Gemma-2b (without SelfExtend) on LongBench, it needs at least 3 A10s to run.~
	~- Unable to use bfloat16 (maybe due to transformers version)~
	~- Using float16 will cause OOM even with 4 A10s~
- \[Oct 20\]: Created README.
- \[Oct 19\]: Reproduced Phi-2 with SelfExtend on LongBench.
- \[Oct 17\]: Reproduced Llama2-7b and Phi-2 on SelfExtend `example.py`.

## Setup

1. Clone this repository on CRC
2. ~Download [this docker image](https://hub.docker.com/r/hoytjin/selfextend_docker/tags) somewhere on CRC~
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

module load cuda
conda activate name_of_your_env

cd /path/to/your/long-context-small-lm/LongLM/
python example.py

echo 'Script completed'
echo 'This script is run with 1 gpus'
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

module load cuda
conda activate name_of_your_env

cd /path/to/your/long-context-small-lm/LongBench/
python pred.py --model phi-2

echo 'Script completed'
echo 'This script is run with 2 gpus'

```

## TODOs

- [x] Add more model configs to `LongBench/config/` for it to be compatible with other models
- [x] Phi1.5-1b
- [ ] Phi1.5-1b SE
- [x] Phi2-3b
- [ ] Phi2-3b SE
- [ ] Gemma-2b
	- "8193 error"
	- May need to use flash attention 2.0
- [ ] Gemma-2b SE
- [ ] Gemma-2-2b
	- "8193 error"
- [ ] Gemma-2-2b SE
- [ ] Llama3.2-1b
- [ ] Llama3.2-1b SE
- [ ] Llama3.2-3b
- [ ] Llama3.2-3b SE
