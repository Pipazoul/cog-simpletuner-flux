## Setup

For users that wish to make use of Docker or another container orchestration platform, see [this document](/documentation/DOCKER.md) first.

1. Clone the repository and install the dependencies:

```bash
git clone https://github.com/bghira/SimpleTuner --branch release
python -m venv .venv
pip3 install -U poetry pip
```

### MacOS (Apple Silicon)

The experience of training a model may be disappointing on Apple hardware due to the lack of memory-efficient attention - things require more VRAM here.

You will require a minimum of 24G of total memory for an SDXL LoRA at a batch size of 1.

To install the Apple-specific requirements:

```bash
poetry install --no-root -C install/apple
```

### Linux + Nvidia/CUDA

The first command you'll run will install most of the dependencies:

```bash
poetry install --no-root
```

#### Optional, possibly not required steps

You will possibly need to install some Linux-specific dependencies (Ubuntu is used here):

> ⚠️ This command can break certain container deployments. If it does, you'll have to redeploy the container.

```bash
apt -y install nvidia-cuda-dev nvidia-cuda-toolkit
```

If you get an error about missing cudNN library, you will want to install torch manually (replace 118 with your CUDA version if not using 11.8):

```bash
pip3 install xformers torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu118 --force
```

Alternatively, Pytorch Nightly may be used (currently Torch 2.3) with Xformers 0.0.23.post1+cu118 (note that this includes torchtriton now):

```bash
pip3 install --pre torch torchvision torchaudio torchtriton --extra-index-url https://download.pytorch.org/whl/nightly/cu118 --force
pip3 install --pre git+https://github.com/facebookresearch/xformers.git@main#egg=xformers
```

If the egg install for Xformers does not work, try including `xformers` on the first line, and run only that:

```bash
pip3 install --pre xformers torch torchvision torchaudio torchtriton --extra-index-url https://download.pytorch.org/whl/nightly/cu118 --force
```

### Linux + AMD / ROCm

Due to `xformers` not supporting the ROCm platform, memory requirements for training will likely be higher than otherwise stated.

To install the ROCm-specific requirements:

```bash
poetry install --no-root -C install/rocm
```

### All platforms

2. Copy `config/config.env.example` to `config/config.env` and then fill in the details.

For both training scripts, any missing values from your user config will fallback to the defaults.

3. If you are using `--report_to='wandb'` (the default), the following will help you report your statistics:

```bash
wandb login
```

Follow the instructions that are printed, to locate your API key and configure it.

Once that is done, any of your training sessions and validation data will be available on Weights & Biases.

4. Launch the `train.sh` script, probably by redirecting the output to a log file:

```bash
bash train.sh > /path/to/training-$(date +%s).log 2>&1
```

> ⚠️ At this point, the commands will work, but further configuration is required. See [the tutorial](/TUTORIAL.md) for more information.

### Run unit tests

To run unit tests to ensure that installation has completed successfully, execute the command `poetry run python -m unittest discover tests/`.
