---
hide: true
toc: true
comments: true
layout: post
title: Scaling Deep Reinforcement Learning to Training on a Private Cluster
description:  Using Ray RLlib to train a deep reinforcement learning agent (PPO) in a custom environment on a private cluster.
image: images/logos/ray.png
categories: [python, ray, rllib, machine learning, reinforcement learning, cluster]
---

Documentation:
* https://docs.ray.io/en/master/cluster/index.html#cluster-index
* https://docs.ray.io/en/master/cluster/cloud.html#local-on-premise-cluster-list-of-nodes
* https://discuss.ray.io/t/getting-started-with-rllib-on-a-private-cluster/683

In this blog post, I use reinforcement learning (RL) to solve a custom optimization task (here, related to coordination in mobile networks).
To this end, I use the scalable RL framework [RLlib](https://docs.ray.io/en/master/rllib.html), 
which is part of [Ray](https://github.com/ray-project/ray), 
and a [custom environment](https://github.com/CN-UPB/DeepCoMP), which implements the [OpenAI Gym](https://gym.openai.com/) interface.
As RL algorithm, I use [proximal policy optimization (PPO)](https://openai.com/blog/openai-baselines-ppo/), which is implemented in RLlib and configured in my environment.

I first show how to train PPO on my environment when running locally.
Then, to speed up training, I execute training on a private/on-premise multi-node cluster.

It took me a while to go from running RLlib and my custom environment locally to getting it to work on a private cluster,
so I'm hoping this guide is useful for anyone in a similar situation.
I do not focus on the specific task or environment but leave this to a future blog post.


# Training an RL Agent Locally

## Setup

Inside a virtualenv, install RLlib with
```
pip install ray[rllib]
```

Then install the custom environment. Here, [DeepCoMP](https://github.com/CN-UPB/DeepCoMP) as described in the readme:
```
pip install deepcomp
```

Test the installation with `deepcomp -h`, which should show the available CLI options.

## Training

Once installation is complete, train a centralized RL agent with PPO in an example scenario.
Note, that training will take a while (around 15min on my laptop), so running the command inside a detachable GNU screen or
tmux session may make sense.
```
deepcomp --agent central --train-steps 100000 --env medium --slow-ues 3
```

This trains a centralized PPO agent for 100k training steps. 
The additional arguments `--env` and `--slow-ues` configure my custom DeepCoMP environment (more about that in another blog post).
During training, updates should be printed.

Furthermore, training progress can be monitored with TensorBoard by running (in a separate terminal):
```
tensorboard --logdir results/PPO/
```
Here, the TensorBoard files are in `results/PPO/`, but this depends on the environment.
TensorBoard can be accessed at localhost:6006.

## Results

* Install RLlib and env
* Run and train PPO with sample command
* Visualize policy and tensorboard, not execution time

# Scaling to Training in a Private Cluster

## Preparations

* Install `ray[rllib]` and the custom environment `deepcomp` on each machine of the cluster. 
  * TODO: do I really need to install both on all machines? And for each update to the env, update all machines manually? Can't be. Maybe just locally or just head node?
* Ensure they can access each other via ssh (e.g., by registering the ssh-key in `authorized_keys`)
* Ensure the `ray` command is available directly after login (active virtualenv)
* Configure `cluster.yaml` to set the number of worker nodes, include head and worker IPs, and configure SSH access
* Set `ray.init(address='auto')` inside the code. This now only works with the cluster, not running locally anymore. Thus, better make it configurable.
* Number of workers: 

## Starting the Ray Cluster

### On the local machine

Start cluster:
```
# start the cluster (non-blocking)
ray up cluster.yaml

# forward the cluster dashboard to the local machine (this is a blocking command)
ray dashboard cluster.yaml
```

View dashboard: http://localhost:8265

Connect to cluster and run command for training.
Note, you can attach but not detach. Thus, better to run this in a screen/tmux session.
```
ray attach cluster.yaml
deepcomp --agent central --train-steps 100000 --env medium --slow-ues 3 --cluster --workers XY
```

Once training completed, detach/close terminal with Ctrl+D.

### Monitoring Training Progress

* Training updates should be printed inside the attached terminal
* On the cluster's head node, `htop` should show `ray::RolloutWorker` running.
* On the cluster's worker nodes, `htop` should show `ray::PPO()::train()` (or similar) to indicate the training is running.
* Monitor progress with Tensorboard running `tensorboard --host 0.0.0.0 --logdir results/PPO/` on the cluster's head node. Then access on `<head-node-ip>:6006`.

### Retrieving Training & Testing Results

From the local laptop, use `ray rsync-down` to copy the result files from the cluster's head node to the local laptop:
```
# ray rsync-down <cluster-config> <source> <target>
ray rsync-down cluster.yaml ~/DeepCoMP/results .
```
Will be copied to local directory into `results`.

### Terminating the Cluster

From the local laptop:
```
ray down cluster.yaml
```

# What Next?

* [Ray cluster documentation](https://docs.ray.io/en/master/cluster/index.html#cluster-index)
* [DeepCoMP GitHub repository](https://github.com/CN-UPB/DeepCoMP)

# Todos:

* Done: Allow configuring `--cluster` and `--result_dir` for DeepCoMP CLI
* Test with multiple nodes on the cluster. Is there a real speedup? Currently, there's just 1 worker node, so it's probably comparable to running directly on a single node.
    * Doesn't work! Update ray first
* Done: Update to latest ray. Fix install with `setup.py` and extra rllib
* Test running on cluster without installing env (and ray?) on workers
* Some basic tests and proper CI (check example command from readme); update Readme with cluster instructions and link to blog; publish new release
* Let Ray team know to distribute the blog post: https://discuss.ray.io/t/use-of-ray-logo-in-blog/797
