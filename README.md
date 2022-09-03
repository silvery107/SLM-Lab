Please find my intro task submission [here](#intro-task-submission).

# SLM Lab
SLM Lab is a software framework for reproducible reinforcement learning (RL) research. It enables easy development of RL algorithms using modular components and file-based configuration. It also enables flexible experimentation completed with hyperparameter search, result analysis and benchmark results.

## Features

- [Modular design](https://slm-lab.gitbook.io/slm-lab/development/modular-lab-components) for building deep RL algorithms
- [Reproducibility  ](https://slm-lab.gitbook.io/slm-lab/using-slm-lab/lab-organization#reproducibility-design) using spec file and git SHA
- [Experiment framework](https://slm-lab.gitbook.io/slm-lab/using-slm-lab/lab-organization#session-trial-and-experiment) with [automatic analysis](https://slm-lab.gitbook.io/slm-lab/analyzing-results/analytics)
- [Extensive benchmark results](https://slm-lab.gitbook.io/slm-lab/benchmark-results/public-benchmark-data)
- Well-tuned algorithm implementations
- Multiple RL environment offerings

## Installation

Clone the repository:

```bash
git clone https://github.com/silvery107/SLM-Lab.git
```

SLM Lab uses PyTorch version 1.3.1 by default. For newer GPU cards, the versions of PyTorch and CUDA that come with SLM Lab default setup above may not be supported. In this case, you may bring-your-own-PyTorch by using [environment-byo.yml](https://github.com/silvery107/SLM-Lab/blob/master/environment-byo.yml) to setup dependencies instead. This installs the same Python modules except for PyTorch and cudatoolkit. Use the following commands:

```bash
# first install the system dependencies
sudo apt-get update && \
  apt-get install -y build-essential \
  curl nano git wget zip libstdc++6 \
  python3-dev zlib1g-dev libjpeg-dev cmake swig python3-opengl libboost-all-dev libsdl2-dev libosmesa6-dev patchelf ffmpeg xvfb && \
  rm -rf /var/lib/apt/lists/*

# setup Conda environment and install everything except PyTorch and cudatoolkit
conda create -n lab python=3.7.3 -y
conda env update -f environment-byo.yml
# install your own pytorch from https://pytorch.org/get-started/locally/
conda activate lab
conda install pytorch==1.7.1 cudatoolkit=11.0 -c pytorch
```

Non-image based environments can run on a laptop. Only image based environments such as the Atari games benefit from a GPU speedup. For these, we recommend 1 GPU and at least 4 CPUs. This can run a single Atari Trial consisting of 4 Sessions.

For desktop, a reference spec is GTX 1080 GPU, 4 CPUs above 3.0 GHz, and 32 GB RAM.

## Quick Start

This quick demo will test that the installation works. We will run DDQN on the Mountain Car environment.

```bash
conda activate lab
python run_lab.py slm_lab/spec/my_mountain_car.json ddqn_per_demo dev
```

This will run a session that trains a DDQN agent on the MountainCar-v0 environment.


> If this quick start works, then SLM Lab is ready for use.


> If you encounter an issue, consult the [Help](https://slm-lab.gitbook.io/slm-lab/resources/help) page.

## Intro Task Submission
**Training**:
```
python run_lab.py slm_lab/spec/my_mountain_car.json ddqn_per_test train
```

**Replay my results**:
- Be sure the `num_envs` is `1` in the replay spec file before running the *enjoy* mode. This is because I use multi envs to boost my training speed and sampling efficiency, but the SLM-Lab seems not support to enjoy "multi envs" or avoid rendering multiple envs in the *enjoy* mode.
```
python run_lab.py slm_lab/spec/my_mountain_car.json ddqn_per_test enjoy@data/ddqn_per_test_2022_09_03_163040/ddqn_per_test_t0_s1_spec.json
```

## Lab Command

### The Lab Command
Before running anything in SLM Lab, be sure to activate the Conda environment:

```
conda activate lab
```

In SLM Lab, everything is run with the lab command with the following form:

```
python run_lab.py {spec file} {spec name} {lab mode}
```

>This command runs any algorithm/environment specified in a spec file in SLM Lab. Spec files are located in the slm_lab/spec/ folder.

### The Spec File
The **spec file** contains the **spec** – a set of fully exposed hyperparameters that configure a run, including the agent, environment, and hyperparameter search. The **spec name** refers to a specific spec in the spec file.


### The Lab Modes

- **dev**: for development with verbose logging, environment rendering, and helpful checks like gradient updates. This is slower but useful for development.
- **train**: for training an agent to completion. This disables the development helper tools and thus runs the fastest.

- **train@{predir}**: for resuming training, e.g. `train@latest` will use the latest run for a spec, and `train@data/reinforce_cartpole_2020_04_13_232521` will use the specified run.
- **enjoy@{session_spec_file}**: for replaying a trained model from a trial-session; `session_spec_file` specifies the spec file from a session, e.g. `enjoy@data/reinforce_cartpole_2020_04_13_232521/reinforce_cartpole_t0_s0_spec.json`.
- **search**: for running an experiment / hyperparameter search.