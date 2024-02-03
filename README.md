# Big Data Architecture with Ansible

This repository contains Ansible playbooks for the automated deployment and configuration of Big Data tools, including:

- `Hadoop`, along with the HDFS, YARN and MapReduce

- `Spark`, along with a PySpark installtion with a Jupyter Notebook Kernel

- `Hive`, along with a MySQL metastore set up

- `Postgres`

- Bioinformatics tools such as `glow` and `hail`. 

# Prerequisites

- Access to a computing cluster

This repo assumes that you intend to install these tools on a computing cluster with a public facing head node (with a public IP address), and a set of private cluster nodes, with only private IP addresses.

(For my use case, I deployed these nodebooks on a 4 node cluster, with the public facing head node running Red Hat Enterprise Linux 8.9, and the 3 cluster nodes running Ubuntu 22.04.2 LTS.)

Edit the `hosts` file with your relevant IP addresses.

- Docker 

This repo was built around running a containerized form of Ansible with SSH access to the head node, where the head node has been set up as a jump host to enable SSH access to the cluster nodes as well. 

[The Docker image used can be found here.](https://hub.docker.com/repository/docker/bushlab/ansible/general)

- Enabling SSH access from the Docker container to the cluster

[ A tutorial on how to accomplish this can be found here.](https://naveenkannan.netlify.app/posts/ansible_cluster/ansible_cluster_config.html)

# File Description

## `hosts`

This file contains the IP addresses and the names of the cluster nodes. Replace these details with your relevant names and addresses.

## `inventory`

This folder contains two `yaml` files:

- `pass.yaml`

This file contains the passwords (and encrypted passwords) needed to run the notebooks. It also contains variables for the IP address of the head node, and its domain name.

- `users.yaml`

This file contains user names along with UIDs and GIDs for addition of users via Ansible. 

# `playbooks`

This file contains the playbooks that will deploy the Big Data tools across your cluster. 

It contains the following sub-files:

## `bioinformatics_playbooks`

This folder contains playbooks for the automated deployment of Glow and Hail. 

These can be deployed only after deployment and set-up of Hadoop, HDFS, YARN and MapReduce.

## `hdfs_playbooks`

This folder contains playbooks for the automated deployment of Hadoop, HDFS, YARN, MapReduce, Spark and Pyspark with a Jupyter Notebook kernel.

`python3.9_workers.yaml` and `rhel_39.python.yaml` are for ensuring the head node and the worker nodes are running the same Python versions.

## `hive_playbooks`

This folder contains a playbook for the automated deployment of Hive, (provided Spark and Hadoop are set up first), along with the setup of a MySQL metastore backend.

## `postgres_playbooks`

This folder contains playbooks for the automated set-up and initialization of Postgres server and database, along with playbooks for the creation of a `postgres` user, and other users as well. 

# Usage

Update the `hosts` file and the `inventory` files with your appropriate use cases. 

Once you've followed the tutorial to ensure passwordless SSH connection to the cluster from within a Docker container, make sure to retain the `.ssh` folder inside the container. 

Run:

```bash
docker run -v /path/to/data_science_ansible/.ssh:/root/.ssh \
                 -v /path/to/data_science_ansible/hosts:/etc/ansible/hosts \
                 -v /path/to/data_science_ansible/playbooks:/etc/playbooks  \
                 -v /path/to/data_science_ansible/inventory:/etc/inventory -it  ansible_docker:v1.1
```