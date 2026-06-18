# Deploying a Node

* Here you will find instructions for the deployment of nodes on **Kubernetes** using **HELM**. This implies that it will be executed from a local machine on a remote server. The local machine and the remote server will communicate via helm. The installation on kubernetes  is compatible with **Google Kubernetes Engine**.

* In order for your node to get permissioned, you need to complete the permissioning process first. In order to understand better what are the types of networks available and the permissioning processes for each network, please check the [README](https://github.com/LACNetNetworks/besu-networks/blob/master/README.md).



## Minimum System Requirements

Recommended hardware features for Besu node:

| Recommended Hardware | On Pro-Testnet | On Mainnet-Omega |
|:---:|:---:|:---:|
| CPU | 2 vCPUs | 4 vCPUs compute optimized|
| RAM Memory | 8 GB | 16 GB |
| Hard Disk | 200 GB SSD | 300 GB SSD |
| IOPs | ----- | 70,000 IOPS READ  50,000 IOPS WRITE |

* **Kubernetes**: Google Kubernestes Engine GKE.

It is necessary to enable the following network ports in the machine in which we are going to deploy the node:

* **Besu Node**:
  * **60606**: TCP/UDP - Port to establish communication p2p between nodes.

  * **4545**: TCP - Port to establish RPC communication. (this port is used for applications that communicate with LACChain and may be leaked to the Internet)


## Pre-requisites

### Install Kubectl ###

For this installation we will use Kubectl. It is necessary to install Kubectl on a **local machine** that will perform the installation of the node on a **kubernetes cluster**.

Following the instructions to [install kubectl](https://kubernetes.io/docs/tasks/tools/) in your local machine.

### Install Helm ###

For this installation we will use Helm. It is necessary to install helm on a **local machine** that will perform the installation of the node on a **kubernetes cluster**.

Following the instructions to [install helm](https://helm.sh/docs/intro/install/) in your local machine.

### Clone Repository ####

To configure and install Besu, you must clone this git repository in your **local machine**.

```shell
$ git clone https://github.com/LACNetNetworks/besu-networks
$ cd besu-networks/helm/
```



## Node Installation ##

### Preparing installation of a new node ###

* There are three types of nodes (Bootnode / Validator / Writer)  that can be created in the blockchain networks orchestrated by LACNet at this moment.

### Values variable ###

*  There are three types of values **bootnode.yml**, **validator.yml** and **writer.yml**.
*  The values ​​you have to set are in the **deploy** section. These are the following:

* **Values**:

  * **network**:    Type Network  - david19-net | protest-net | main-net.

  * **typenode**:   Type of Node  - writer | validator | bootnode.

  * **publicIP**:   TCP Public IP Ingress.

  * **p2p - host**: P2P Public IP Egress.  

  * **p2p - port**: P2P PORT  - Default (60606).

  * **workerName**: Name of the node worker where always the pod will be installed.

  * **dnsName**:    Organization domain name (e.g. lacchain.com). 

  * **nodeName**:   Name you want for your node in the network monitoring tool.

  * **nodeEmail**:  email address you want to register for your node in the network monitoring tool. It's a good idea to provide the e-mail of the technical contact identified or to be identified in the registration form as part of the on-boarding process.


### Set value to environment variable ###
* **TCP Public IP Ingress**: Generate a *_static public IP_* in your cloud provider. Then replace the public ip in the load balance *_(loadBalancerIP)_* service manifest. finally update the **publicIP** environment variable with this IP.

* **P2P Public IP Egress**: Outgoing p2p traffic to synchronize besu nodes. This is the permissioned IP for the  network. Therefore, the pod must always be installed on the same worker node so that the IP does not change.
We obtain the name and IP of the cluster nodes with the following command.
    
    $ kubectl get nodes -o wide

![workers](/docs/images/workes-k8s.PNG)

 We choose a worker and update the **"nodeName"** value in the manifest of the pod we are going to deploy. finally update the **p2p - host** environment variable with worker IP external .
 
 <span style="color:yellow "> *_Note:  We validate that the pod has been deployed in the selected worker with the following command._*<span> 
    
    $ kubectl get pod -o wide 



### Deploying the new node ###

* Depending type node you want to deploy , you need  execute  the following command:

    <span style="color:yellow "> Note: This deployment is compalitible only  Google Kubernestes Engine GKE<span> 



 * To deploy a **Node Writer**     
      
      ```shell
      $ helm install <chart-name>  ./charts/besu-node --namespace  <namespace-name> --create-namespace --values ./values/writer.yml 
    
      ```
 * To deploy a **Node Bootnode**     
      
      ```shell
      $ helm install <chart-name> ./charts/besu-node --namespace  <namespace-name> --create-namespace --values ./values/bootnode.yml 
      ```
 * To deploy a **Node Validator**     
      ```shell
      $ helm install <chart-name>  ./charts/besu-node --namespace  <namespace-name> --create-namespace --values ./values/validator.yml 
      ```


* e.g. deploy **Node Writer** on **Mainnet-Omega**  network
 
    ```shell
      $ helm install lacnet-writer-1 ./charts/besu-node --namespace  lacchain-main-net --create-namespace --values ./values/writer.yml
    ```
* At the end of the installation, if everything worked a BESU pod will be created managed by kubernetes with **Running** status. Aditional  objects created are namespace, service load balancer, configmap, and volume.


Don't forget to write down your node's "enode" :
```shell
  $ curl -X POST --data '{"jsonrpc":"2.0","method":"net_enode","params":[],"id":1}' http://<PUBLIC_IP>:4545

```
Result:
```
"result" : "enode://d837cb6dd3880dec8360edfecf49ea7008e50cf3d889d4f75c0eb7d1033ae1b2fb783ad2021458a369db5d68cf8f25f3fb0080e11db238f4964e273bbc77d1ee@104.197.188.33:60606"

```


* In order to be permissioned, now you need to follow [administrative steps of the permissioning process](https://github.com/LACNetNetworks/besu-networks/blob/master/README.md).



## Node Configuration

### Configuring the Besu node file ###

The default configuration should work for everyone. However, depending on your needs and technical knowledge you can modify your  node's settings in values folder  `writer.yml bootnode.yml validator.yml`,  for RPC access or authentication. Please refer to the [reference documentation](https://besu.hyperledger.org/en/21.1.6/Reference/CLI/CLI-Syntax/).

	
## Checking your connection

Once you have been permissioned, you can check if your node is connected to the network properly.

Check that the node has stablished the connections with the peers:

```shell

$ curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":1}' http://<PUBLIC_IP>:4545
```

You should get a result like this:

![Connections](/docs/images/log_connections.PNG)
x
Now you can check if the node is syncing blocks by getting the log:

```shell
$ kubectl logs <pod name> -c <container name> -f -n <namespace>
```

You should get something like this:

![Log of latest blocks](/docs/images/log_blocks.PNG)

if you need to update the node, try redeploy the besu node: e.g. **Node Writer**

```shell
$ $ helm upgrade <chart-name> ./charts/besu-node --namespace  <namespace-name>  --values ./values/writer.yml 
```

If any of these two checks doesn't work, try to restart the besu service: e.g. **Node Writer**

```shell
$ kubectl delete pod <pod name> -n <namespace>
```

If that doesn't solve the problem, [open a ticket](https://lacnet.lacchain.net/support/) if you already have a membership or contact us at tech.support@lac-net.net.
	
## Deploying Dapps

For a quick overview of some mainstream tools that you can use to deploy Smart Contracts, connect external applications and broadcast transactions to the LACChain Besu Network, you can check our [guide](https://github.com/LACNet-Networks/besu-pro-testnet/blob/master/docs/DEPLOY_APPLICATIONS.md). We also recommend you [our tutorials to deploy your first smart contracts](https://github.com/LACNet-Networks/gas-management/tree/master/docs/tutorial).

## Contact

For any issues, you can either go to [issues](https://github.com/LACNet-Networks/besu-pro-testnet/issues) or e-mail us at tech-support@lac-net.net. Any feedback is more than welcome!

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
