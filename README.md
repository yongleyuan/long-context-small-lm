# Long Context in Small LMs with SelfExtend

Course project for [CSE 60556] Large Language Models

## Setup

1. Clone this repository using `git clone ...`
2. Update all submodules `git submodule update --init --recursive`
3. Checkout my branch `git checkout selfextend-jack` for both directories
2. Create a new conda environment with `conda create -n name_of_your_env python=3.10`, activate it `conda activate name_of_your_env`, and install all modules `pip install -r requirements.txt`
	- You may need to manually install torch==2.3.0+cu121 *first* with `pip install torch==2.3.0+cu121 -f https://download.pytorch.org/whl/torch_stable.html`

## Usage

`LongBench` and `LongLM` have to be in the same directory to apply SelfExtend.

I have added Python script arguments in `LongBench/pred.py` and `LongBench/eval.py` to accept `--se`. Including this option will apply SelfExtend to LongBench scripts.

### GPU usage

Most job could run with **2 A10 GPU cards**.

## Configuration

Checkout the configuration files in `LongBench/config/` to find models' names and their pre-trained context lengths.

### Addition / Modification

When you add a new model, first add its context length and path to `LongBench/config/model2maxlen.json` and `LongBench/config/model2path.json` respectively.

After that, add your new model to `LongBench/pred.py`. Their are several places you need to modify.
1. In `parse_args` function, add your model name to `choices`, make sure it is the same as the name in config files.
2. In `load_model_and_tokenizer`, add your model so that it can load it here. In most cases you only need to add your model to one of the `if` or `elif` statements, but if none of them works out, you might need to write your own model and tokenizer initialization codes.

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
python pred.py --model phi2-3b

echo 'Script completed'
echo 'This script is run with 2 gpus'
```

## Tricks / Known issues

### Use interactive GPU frontend

Use `qrsh -q gpu -l gpu_card=1` to attach to an interactice frontend on GPU nodes. Then activate your conda environment and run your job directly without submitting job scripts (i.e. `python pred.py ...`). This allows you to have an idea if the script is run successfully. If not, make adjustments; else, submit your job script.

You can also remove some of the datasets used, and just try the script with a few datasets. This reduces run time.

### Flash attention

In most cases, flash attention should **not** be used. If the model can only be run with flash attention, make sure it is enabled both when initializing models and when applying SelfExtend (they have to match with each other).

## Tasks

### Reproduce 

**ALL SHOULD BE DONE BY THE END OF 11/23**

#### SLMs with short context length

**Phi1.5-1b:**
- [x] Without SE
- [ ] With SE (Jack)

**Phi2-3b:**
- [x] Without SE
- [x] With SE

**Gemma-2b:**
- [x] Without SE
- [x] With SE

~**Gemma2-2b:**~

**Phi3-mini-4k:**
- [ ] Without SE (Jialiang)
- [ ] With SE (Jialiang)

**Phi3-mini-128k:**
- [ ] Without SE (Jialiang)
- [ ] With SE (Jialiang)

#### SLMs with long context length

**Llama3.2-1b:**
- [ ] Without SE (Jack)
- [ ] With SE (Jack)

**Llama3.2-3b:**
- [ ] Without SE (Jack)
- [ ] With SE (Jack)

**Qwen2-0.5b:**
- [ ] Without SE (Sidney)
- [ ] With SE (Sidney)

**Qwen2-1.5b:**
- [ ] Without SE (Sidney)
- [ ] With SE (Sidney)

### Hyperparameter calculation

TBD
