# Tutorial on using flower Simulation in a multi-node environment

  

This is an extension of the offical pytorch simulation of flower from [Documentation](https://github.com/adap/flower/tree/main/examples/simulation-pytorch)

  

## Aim: To use a multi-node GPU setup in flower simulation to reduce the training time and better management of resources.

  

## Setting up the Environment

  

1. Clone this repository using command

```shell

git clone https://github.com/nclabteam/flwr-multi-node-simulations.git

```

2. After the repository is cloned, open the cloned repository in terminal.

```shell

cd flwr-multi-node-simulations/

```

  

3. Ensure pip is installed if not install using

```shell

sudo apt install python3-pip

```

  

4. We are using venv package to create virtual environent you can use any virtual environment libary to proceed. Run the below command in terminal to install venv.

```shell

sudo apt install python3.10-venv

```

  

5. Use python venv to create a virtual environment for our project

  

```shell

python3 -m venv venv-multi-node

```

Where `venv-multi-node` is the name of the virtual environment that we want to create. We can choose any name that we like instead of `venv-multi-node`. After this step a new directory (venv-multi-node) will be created.

  

6. For using virtual environment we need to activate the environment first.

```shell

source venv-multi-node/bin/activate

```

7. Now we can install the project requirements or dependencies inside virtual environment using terminal as:

```shell

pip install -r requirements.txt

```

  

### Run Federated Learning Example Single Node
```shell

python3 sim.py --num_rounds= 11

```
You can adjust the CPU/GPU resources you assign to each of your virtual clients. By default, your clients will only use 1xCPU core. For example:
```bash

# Will assign 2xCPUs to each client

python3  sim.py  --num_cpus=2

# Will assign 2xCPUs and 20% of the GPU's VRAM to each client

# This means that you can have 5 concurrent clients on each GPU

# (assuming you have enough CPUs)

python3  sim.py  --num_cpus=2  --num_gpus=0.2

```
  

### Run Federated Learning Example Multi-Node

1. Consider one node/pc as head node, and in the terminal after activating virtual environment first run the command as
```bash

ray start --head

```

`--head` specifies that we are using this node as cluster head.

After there will be some instructions containing the **ip:port** of the head and how to connect other nodes with this cluster like:

```

To add another node to this Ray cluster, run

ray start --address='<ip>:6379' <ip> will be the ip of your head node and 6379 is default port for ray

To connect to this Ray cluster:

import ray

ray.init()

To submit a Ray job using the Ray Jobs CLI:

RAY_ADDRESS='http://127.0.0.1:8265' ray job submit --working-dir . -- python my_script.py

See https://docs.ray.io/en/latest/cluster/running-applications/job-submission/index.html

for more information on submitting Ray jobs to the Ray cluster.

To terminate the Ray runtime, run

ray stop

To view the status of the cluster, use

ray status

To monitor and debug Ray, view the dashboard at

127.0.0.1:8265

```
We can check the ray dashboard at http://127.0.0.1:8265

You can adjust the total CPU/GPU resources that this node can use as:
```bash

ray  start  --head  --num_cpus  16  --num_gpus  1

```
By default, ray intilizes the node with all resources.

Take a look at the [Ray documentation](https://docs.ray.io/en/latest/ray-core/configure.html) for more details on how you can customise client node resources

2. Now repeat steps 1-7 to clone the repo and setup virtual environment on ther nodes/pc's

4. Then we can connect each node/pc to this head node as :

```bash

ray start --address <ip>:6379

```

`<ip>` is the ip address of head node provided by ray head node in step above

You can also consider running this node with less resources as

```bash

ray start --address '<ip>:6379 --num_cpus 16 --num_gpus 1

```

So this node will be intilized with 16 cpus and 1 GPU if avalaible.

4. After connecting all the nodes/pc's with the head node we can check the total resources in the cluster by visiting http://127.0.0.1:8265/#/overview

5. Finally to run the federated script in multi-node environment just run the script on the **`head node`**:

```bash

python3 sim.py --num_rounds= 11 --multi_node true

```

rest of the things will be handled by ray.