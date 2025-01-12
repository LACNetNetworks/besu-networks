## Overview

* LACChain Networks are blockchain networks initially developed within the [LACChain project](https://www.lacchain.net/home) and now orchestrated by the non-profit legal vehicle [LACNet](https://lacnet.lacchain.net/). These networks are classified as permissioned public blockchain infrastructure, as defined by the standard [ISO/TC 307](https://www.iso.org/committee/6266604.html). LACChain Networks follow the [LACChain Framework](https://publications.iadb.org/en/lacchain-framework-permissioned-public-blockchain-networks-blockchain-technology-blockchain).

* As public blockchain networks, LACChain Networks are open to any entity in Latin America and the Caribbean. As permissioned networks, entities must be authenticated and commit to comply with regulation in order to be permissioned. LACChain Networks have no trasaction fees.

* The Testnet networks are free. In order to deploy a node, it is required to file a very short [registration agreement form](https://github.com/LACNetNetworks/besu-networks/blob/master/testnet/agreement_form/agreement_form.md) as well as reading, understing, and agreeing with the terms of reference. Find the details for the permissioning process [here](https://github.com/LACNetNetworks/besu-networks/blob/master/testnet/permissioning_process/permissioning_process_testnet.md). 

* The Mainet networks are [membership-based](https://lacnet.lacchain.net/contrata-tu-membresia/). These networks are aimed to ensure relisience and accountability via Service Level Agreements between any entity operating a node and LACNet. Find the details of the permissioning process [here](https://github.com/LACNetNetworks/besu-networks/blob/master/mainnet/permissioning_process/permissioning_process_mainnet.md).

* The nodes in the LACChain networks can be classified into two groups, according to their relevance for the functioning of the network. The two groups are core and satellite nodes. In each of these two groups there are also two different types of nodes, according to the specific taks they can perform. Core nodes are classified into validator and boot nodes, and satellite nodes are classified into writer and observer nodes. For more information you can go to [Topology and Architecture](https://github.com/LACNetNetworks/besu-networks/blob/master/docs/TOPOLOGY_AND_ARCHITECTURE.md).

* The LACChain Networks avaliable in this repository are based on [Hyperledger Besu](https://www.hyperledger.org/projects/besu), an open-source, mainnet compatible, Java based, and Apache 2.0 licensed Ethereum client. For more information you can read the [code](https://github.com/hyperledger/besu) and the [documentation](https://github.com/hyperledger/besu-docs).

* These LACChain Networks use [IBFT2.0](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/IBFT/) consensus protocol for the validation of transactions and generation of new blocks. Additionally, we have developed a [protocol to rotate validador nodes](https://github.com/LACNetNetworks/rotation-validator) in a way that maximizes performance and decentralization.

* The GAS is distributed in these networks following the [GAS distribution mechanism](https://github.com/LACNetNetworks/gas-management). It is important that you go through this documentation before sending transactions to the network.

* We have created two guides to help you [Deploy your Dapp on LACChain](https://github.com/LACNetNetworks/besu-networks/blob/master/docs/DEPLOY_APPLICATIONS.md) and [provide your Dapp with a suitable archiecture](https://github.com/LACNetNetworks/besu-networks/blob/master/docs/DAPP_ARCHITECTURE.md). We have also developed [several tutorials](https://github.com/LACNetNetworks/gas-management/tree/master/docs/tutorial) to [deploy your first ERC20 and notarization smart contracts](https://github.com/LACNetNetworks/gas-management/blob/master/docs/tutorial/Deploy_SmartContract.md) as well as more complex smart contracts that implement elements of the [LACChain ID framework](https://publications.iadb.org/en/lacchain-framework-permissioned-public-blockchain-networks-blockchain-technology-blockchain).

* You can also benefit from the [transaction explorer](https://explorer.lac-net.net/) and the [Ethstats dashboard](https://dashboard.lac-net.net/).

* Every entity running a node in the LACChain networks is listed in the [list of permissioned nodes](https://github.com/lacchain/besu-network/blob/master/NODE_LIST.md).

* For additional information, please check the [FAQ](https://github.com/LACNetNetworks/besu-networks/blob/master/docs/FAQ.md).

## List of Networks Available and Permissioning Process

At present, there are three LACChain Networks using Hyperledger Besu as the underlying technology orchestrated by LACNet. The permissioning process to be accomplished before deploying the node varies depending on the network.

**Mainnet Omega:** The Mainnet Omega is the network recommended for all the initiatives in production. It is aimed at guarateeing reliability and accountability. Writer nodes are required to pay a membership to contribute to the technical teams of the non-profit orquestrator LACNet that provide support to node operators. The permissioning process requires signing the [Adscription Agreement (SLA)](https://github.com/LACNetNetworks/besu-networks/tree/master/mainnet/adcription_contracts) and accomplishing the payment of the membership. To initiate the permissioning process, [fill the permissioning form](https://lacnet.lacchain.net/lead-form-eng/) and a focal point from the membership team will reach out to you to help you thorugh the process. 

**Pro-Testnet:** The Protestnet is the network is recommended for testing a solution before jumping into the Mainnet Omega. This network could be rewritten or interrupted following tests and simulation needs. There is no payment needed to deploy nodes in this network, nor any fee of any kind for sending transactions. The permissioning process requires filling an [Agreement Form](https://lacnet.lacchain.net/wp-content/uploads/2022/03/LACChain-Node-Authorization-form.pdf) as well as reading, understanding, and agreeing with the [terms and conditions](https://github.com/LACNetNetworks/besu-networks/tree/master/terms_and_conditions_testnets). After June 15th the GAS distribution mechanism will be implemented in the Pro-Testnet. All the transactions that do not comply with the [GAS distribution mechanism](https://github.com/LACNetNetworks/gas-management) will be rejected by the network. For more information check [see our official communication](https://live-lacchain.pantheonsite.io/sites/default/files/2022-05/The%20Protestnet%20is%20changing_0.pdf).

Once you have accomplished the permissioning successfully, you can proceed to deploy your node. If you have questions, do not hesitate to reach out to tech.support@lac-net.net.

## Deploy a Node

To deploy a node in the blockchain networks orchestrated by LACNet, go [HERE](https://github.com/LACNetNetworks/besu-networks/blob/master/DEPLOY_NODE.md). 
