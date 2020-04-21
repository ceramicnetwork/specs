# Anchor Service

> This document describes the standard anchor service interface used by a Ceramic node to anchor document updates on a blockchain.

## Overview

Anchor services play a crutial role in the Ceramic network. The allow Ceramic to scale effectively by reducing the need for individual users to anchor their documents in singular blockchain transactions. Instead an anchor service can anchor many document updates in a single blockchain transaction. A general description of how this works can be found in the [Blockchain anchoring](./README.md#blockchain-anchoring) section. The data format of these anchors are described in the [Anchor records](./README.md#anchor-records) section. This document



## A Merkle Tree in IPLD





## Sorting of Leaf Nodes





## Endpoints

`/api/v0/`



### Create Anchor Request





### Get anchor request information





## Service Policy tile



##### Schema

```json
{
}
```



##### Example

```json
{
}
```

