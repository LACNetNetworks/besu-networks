# Deploying a Node

* Quickly deploy a writer node using **docker** [here](https://github.com/LACNetNetworks/besu-networks/blob/master/DEPLOY_NODE_DOCKER.md). **Only for test networks.**

* If you want to deploy a node on **kubernetes** following the instructions [here](https://github.com/LACNetNetworks/besu-networks/blob/master/DEPLOY_NODE_HELM.md)

* Below you will find instructions for the deployment of nodes using **Ansible**. This implies that it will be executed from a local machine on a remote server. The local machine and the remote server will communicate via ssh.


* The installation with ansible provided is compatible with **Ubuntu** and **Centos7**.

* During the process of node deploying, you will be asked about the network in which you would like to deploy your nodes. In order for your node to get permissioned, you need to complete the permissioning process first. In order to understand better what are the types of networks available and the permissioning processes for each network, please check the [README](https://github.com/LACNetNetworks/besu-networks/blob/master/README.md).

* If an organization intends to create private channels, we have facilitated the integration with the private transaction manager [Tessera](https://docs.tessera.consensys.net/en/stable/). Tessera nodes must be deployed in a different instance (virtual machine), and therefore in order to enable Tessera nodes for private transactions you will require two virtual machines. It is worth mentioning that **Tessera is optional** and entities can join the networks only with Besu nodes.

## Minimum System Requirements

Recommended hardware features for Besu node:

| Recommended Hardware | On Mainnet-Omega | On Open Pro-Testnet | On Legacy Pro-Testnet |
|:---:|:---:|:---:|:---:|
| CPU | 4 vCPUs compute optimized | 4 vCPUs compute optimized | 2 vCPUs |
| RAM Memory | 16 GB | 16 GB | 8 GB |
| Hard Disk | 375 GB SSD | 375 GB SSD | 250 GB SSD |
| IOPs | 70,000 IOPS READ  50,000 IOPS WRITE | 70,000 IOPS READ  50,000 IOPS WRITE | 15,000 IOPS READ  5,000 IOPS WRITE |

* **Operating System**: Ubuntu 16.04, Ubuntu 18.04, Ubuntu 20.04, Ubuntu 22.04, Centos7, always 64 bits

It is necessary to enable the following network ports in the machine in which we are going to deploy the node:

* **Besu Node**:
  * **60606**: TCP/UDP - Port to establish communication p2p between nodes.

  * **4545**: TCP - Port to establish RPC communication. (this port is used for applications that communicate with LACChain and may be leaked to the Internet)

* **Tessera Node (Optional component for private transactions)**: 
  * **4040**: TCP - Port to communicate with other Tessera nodes.
  
  * **4444**: TCP - Port for communication between Besu and Tessera.

## Pre-requisites

### Install Ansible ###

For this installation we will use Ansible. It is necessary to install Ansible on a **local machine** that will perform the installation of the node on a **remote machine**.

Following the instructions to [install ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) in your local machine.

```shell
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
```

### Clone Repository ####

To configure and install Besu and Tessera, you must clone this git repository in your **local machine**.

```shell
$ git clone https://github.com/LACNet-Networks/besu-networks
$ cd besu-networks/
```

### Obtain SSH access to your remote machine ###

Make sure you have SSH access to nodes you're setting up. This step will vary depending on your context (physical machine, cloud provider, etc.). This document assumes that you are able to log into your remote machine using the following command: `ssh remote_user@remote_host`.

## Node Installation ##

### Preparing installation of a new node ###

* There are three types of nodes (Bootnode / Validator / Writer) + the optional Tessera (for private side-chains) that can be created in the blockchain networks orchestrated by LACNet at this moment.

* After cloning the repository on the **local machine**, enter it and create a copy of the `inventory.example` file as `inventory`. Edit that file to add a line for the remote server where you are creating the new node. You can do it with a graphical tool or inside the shell:

    ```shell
    $ cd besu-networks/
    $ cp inventory.example inventory
    $ vi inventory
    [node]
    192.168.10.72 node_ip=your.public.node.ip node_name=my_node_name node_email=your@email
    ```

Consider the following points:
- Place the new line in the section corresponding to: `[node]`.
- The first element on the new line is the IP or hostname where you can reach your remote machine from your local machine.
- The value of `password` is the password that will be used to set up Tessera, for private transactions.
- The value of `node_name` is the name you want for your node in the network monitoring tool.
- The value of `node_email` is the email address you want to register for your node in the network monitoring tool. It's a good idea to provide the e-mail of the technical contact identified or to be identified in the registration form as part of the on-boarding process.

* **[(Optional) Installation of Tessera (for private side-chains)]** 

  * In your `inventory` file add a line below [tessera] role. This new line is the IP or hostname where you can reach your remote machine from your local machine. In this Ip or hostname will be installed Tessera node. 
  * Additionally, change `tessera` variable located under the [all: vars] tag in same inventory file to `true`.
  * The inventory file looks like similar to:
  ```lang-toml
     [tessera]
     127.0.0.1 ---> Change for your IP Tessera instance
     
	 [all:vars]
     password=default_password
     node_email=default@email
     ...
     tessera=false ---> Set to true to install Tessera

### Deploying the new node ###

* When running the script, type which kind of node are you deploying and make sure to type the network where the node will be deployed:
```
[0]:validator
[1]:boot
[2]:writer
[3]:tessera
Please, choose which type of node are you deploying:

[0]:mainnet-omega
[1]:open-protestnet

Please, choose in which network are you deploying:
```
So, if you want to deploy a writer node on mainnet-omega, first type 2 for writer, next it will be 0 for mainnet-omega and for open-protestnet 1.

* To deploy a **node** with/without **tessera node**  execute the following command in your **local machine**. If needed, don't forget to set the private key with option `--private-key` and the remote user with option `-u` to SSH connection:

	```shell
	$ ansible-playbook -i inventory --private-key=~/.ssh/id_rsa -u remote_user site-lacchain-node.yml
	```
* [**in case you have previously deployed a writer node without tessera**] To deploy a **tessera node** execute one of the following command in your **local machine**. If needed, don't forget to set the private key with option `--private-key` and the remote user with option `-u` to SSH connection:

	```shell
	*Tessera*
	$ ansible-playbook -i inventory --private-key=~/.ssh/id_rsa -u remote_user site-lacchain-tessera.yml
	```

* At the end of the installation, if everything worked a BESU service will be created managed by Systemctl with **started** status.

Don't forget to write down your node's "enode" from the log by locating the line that looks like this:
```
TASK [lacchain-validator-node : print enode key] ***********************************************
ok: [x.x.x.x] => {
    "msg": "enode://cb24877f329e0e3fff6c7d7b88d601b698a9df6efbe1d91ce77130f065342b523418b38cb3c92ea3bcca15344e68c7d85a696eb9f8c0152c51b9b7b74729064e@a.b.c.d:60606"
}
```

* If everything worked, an TESSERA service **(if it was chosen)** and a BESU service managed by Systemctl will be created with **started** status on each instance.
* After installation has finished you will have nginx installed on each instance chosen; it will be up and running and will allow secure and encrypted RPC connections (on the default 443 port). Certificates used to create the secure connections are self signed; it is up to you decide another way to secure RPC connections or continue using the provided  default service.
* In order to be permissioned, now you need to follow [administrative steps of the permissioning process](https://github.com/LACNetNetworks/besu-networks/blob/master/README.md).
* Once you are permissioned, you can verify that you are connected to other nodes in the network by following the steps detailed in [#issue33](https://github.com/lacchain/besu-network/issues/33).

## Node Configuration

### Configuring the Besu node file ###

The default configuration should work for everyone. However, depending on your needs and technical knowledge you can modify your local node's settings in `/root/lacchain/config.toml`, e.g. for RPC access or authentication. Please refer to the [reference documentation](https://besu.hyperledger.org/en/21.1.6/Reference/CLI/CLI-Syntax/).

### Start up your node ###

Once your node is ready, you can start it up with this command in **remote machine**:

* For Besu instance:
```shell
<remote_machine>$ service pantheon start
```

* For Tessera instance:
```shell
<remote_machine>$ service tessera start
```

### Node Operation ###

 * If you need to restart the services, you can execute the following commands:

```shell
<remote_machine>$ service tessera restart
<remote_machine>$ service pantheon restart
```

### Updates ###
  * You can update your node, by preparing your inventory with:
    * For Besu
	```shell
	[node] #here put the role you are going to update
	35.193.123.227 
	```

	Optionally you can choose the sha_commit of the version you want to update; with Besu is is only neede to specify the version:
	```shell
	[node] #here put the role you are gong to update
	35.193.123.227 besu_release_version='22.1.0'
	```
	Current Besu versions obtained from: https://pegasys.tech/solutions/hyperledger-besu/
	Tested BESU versions: 
	21.1.6
	20.10.4
	1.5.3
	1.5.2
	1.4.4
	1.3.6

	Current orion commit sha versions obtained from: https://github.com/PegaSysEng/orion/releases
	Tested orion versions: 
	1.6.0
	1.5.2
	1.3.2
	1.4.0

	Replace the ip address with your node ip address.

	Now according to the role your node has, type one of the following commands on your terminal:
	```shell
	$ ansible-playbook -i inventory --private-key=~/.ssh/id_rsa -u remote_user site-lacchain-update-node.yml 
	```
	
## Checking your connection

Once you have been permissioned, you can check if your node is connected to the network properly.

Check that the node has stablished the connections with the peers:

```shell
$ sudo -i
$ curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":1}' localhost:4545
```

You should get a result like this:

![Connections](/docs/images/log_connections.PNG)

Now you can check if the node is syncing blocks by getting the log of the last 100 blocks:

```shell
$ tail -100 /root/lacchain/logs/pantheon_info.log
```

You should get something like this:

![Log of latest blocks](/docs/images/log_blocks.PNG)

If any of these two checks doesn't work, try to restart the besu service:

```shell
$ service pantheon restart
```

If that doesn't solve the problem, [open a ticket](https://lacnet.lacchain.net/support/) if you already have a membership or contact us at tech-support@lac-net.net.
	
## Deploying Dapps

For a quick overview of some mainstream tools that you can use to deploy Smart Contracts, connect external applications and broadcast transactions to the LACChain Besu Network, you can check our [guide](https://github.com/LACNet-Networks/besu-networks/blob/master/docs/DEPLOY_APPLICATIONS.md). We also recommend you [our tutorials to deploy your first smart contracts](https://github.com/LACNet-Networks/gas-management/tree/master/docs/tutorial).

## Contact

For any issues, you can either go to [issues](https://github.com/LACNet-Networks/besu-networks/issues) or e-mail us at tech.support@lac-net.net. Any feedback is more than welcome!

## Copyright 2022 LACNet

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
