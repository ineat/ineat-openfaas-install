# OpenFaas on Docker Swarm Tutorial
This project contains VagrantFile and Ansible Playbooks wich can be used to create a local cluster with OpenFaas deployed on it

[OpenFaas on Swarm Cluster](https://blog.ineat-conseil.fr/2019/01/serverless-sur-raspberry-pi-avec-docker-swarm-et-openfaas-partie-1-installation-dopenfaas/)

## How to add worker

> In vagrant-config.yml, add new worker settings. For example :

```yaml
...
    worker-3:
        ip: "10.0.3.5"
        config: 
            cpus: 1
            memory: 512
            image: "ubuntu/xenial64"
            network: "private_network"
...
```

> In hosts.yml, add worker IP in workers. For example :
```yaml
[workers]
...
10.0.3.5
```

## How to start

> Clone this project
> Start cluster creation with the following command :

```shell
vagrant up
```