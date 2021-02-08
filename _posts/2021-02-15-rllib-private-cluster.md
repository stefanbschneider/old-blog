---
hide: true
toc: true
comments: true
layout: post
title: Scaling Deep Reinforcement Learning: From Local Execution to Running a Cluster
description:  Using Ray RLlib to train a deep reinforcement learning agent (PPO) in a custom environment on a private cluster.
image: images/logos/bootstrap.png
categories: [python, ray, rllib, machine learning, reinforcement learning, cluster]
---

Documentation:
* https://docs.ray.io/en/master/cluster/index.html#cluster-index
* https://docs.ray.io/en/master/cluster/cloud.html#local-on-premise-cluster-list-of-nodes
* https://discuss.ray.io/t/getting-started-with-rllib-on-a-private-cluster/683

# Training an RL Agent Locally

* Install RLlib and env
* Run and train PPO with sample command
* Visualize policy and tensorboard, not execution time

# Scaling to Training in a Private Cluster

## Preparations

* Install `ray[rllib]` and the custom environment `deepcomp` on each machine of the cluster. 
  * TODO: do I really need to install both on all machines? And for each update to the env, update all machines manually? Can't be. Maybe just locally or just head node?
* Ensure they can access each other via ssh (e.g., by registering the ssh-key in `authorized_keys`)
* Ensure the `ray` command is available directly after login (active virtualenv)
* Configure `cluster.yaml`
* Set `ray.init(address='auto')` inside the code. This now only works with the cluster, not running locally anymore. Thus, better make it configurable.
* Number of workers: 

## Starting the Ray Cluster

### On the local machine

Start cluster:
```
ray up cluster.yaml
```

View dashboard: http://localhost:8265 (doesn't work!)

Connect to cluster and run command for training.
Note, you can attach but not detach. Thus, better to run this in a screen/tmux session.
```
ray attach cluster.yaml
deepcomp --args
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
ray rsync-down cluster.yaml ~/DeepCoMP/results/ results
```

### Terminating the Cluster

From the local laptop:
```
ray down cluster.yaml
```

# Todos:

* Update to latest ray. Fix install with `setup.py` and extra rllib
* Allow configuring `--cluster` and `--results` for DeepCoMP CLI
* Test with multiple nodes on the cluster. Is there a real speedup? Currently, there's just 1 worker node, so it's probably comparable to running directly on a single node.

