# Tile doctype

> Tile documents used to describe schemas, metadata, services, access control, etc.

Tiles are a flexible way to create single or multi-party statements and can be used to define infrastructure descriptions, service agreements, routing information, data schema definitions, access control permissions, and much more.

## Index

- [Genesis record](#genesis-record)
- [Signed record](#signed-record)
- [Update rules](#update-rules)
- [Examples](#examples)
    - [Collection Policy](#collection-policy)
    - [Service Policy](#service-policy)
    - [Privacy Policy](#privacy-policy)

## Genesis record

**Doctype name:** `tile`

The genesis record of a tile document is quite flexible. The `content` property contains the data of the tile and can be set to any json data. The `owners` property must contain an array of DIDs (at least one). It specifies the `owners` who are allowed to update the document (note that a signature from all the owners is needed in order to make a valid update to a tile). In the example below the 3ID DID method is used, but Ceramic can support other DID methods as well. As opposed to the `3id` and `account-link` doctypes the genesis record of a `tile` is signed by the DID specified in `owners`.

Tile genesis records are stored in IPFS using the [`dag-cbor`](https://github.com/ipld/js-ipld-dag-cbor/) IPLD format.

```JSON
{
  "doctype": "tile",
  "owners": ["did:3:<CID>"],
  "content": ...
}
```

## Signed record

Signed records in 3ID are stored in IPFS using the `dag-jose` or `dag-cose` formats. These formats allow for IPLD objects to be stored along with a standardized signature format. They also allow DAG objects to be encrypted. The update to the document is encoded using [json-patch](https://github.com/Starcounter-Jack/JSON-Patch).

```JSON
{
  "next": <CID-previous-record>,
  "content": <JSON-patch-object>
}
```

## Update rules

An update to a tile can change anything in the `content` of the tile document. If there are multiple owners only the first *signed record* needs to contain the json-patch that changes the document. The other owners can simply sign a record that only contains the `next` pointer to the previous signed record.

## Examples

Below there are three examples of possible tiles, Collection Policy, Service Policy, and Privacy Policy. These tiles in particular are useful when specifying a user cenrtic data storage system.

### Collection Policy

The **Collection Policy tile** represents a *collection* of data, or rather a *collection* of databases that has specific schemas. This tile also contain tags which describe what type of data is being stored within the collection.

**Example:**

```JSON
{
  "doctype": "tile",
  "owners": ["did:3:bafyappDeveloper..."],
  "content": {
    "databases": [{
      "type": "orbitdb",
      "store": "kvstore",
      "access": "legacy-ipfs-3box",
      "schema": ...
    }, {
      "type": "orbitdb",
      "store": "feedstore",
      "access": "thread-access"
      "schema": ...
    }],
    "tags": ["profile"]
  }
}
```

### Service Policy

A **Service Policy tile** is used to describe a simple service, usually in the form of a function that takes an input and produces an output. But could also describe more complex services such as database hosting etc.

**Example:**

This is an example of how the service tile for the Ceramic anchoring service could look like.

```JSON
{
  "doctype": "tile",
  "owners": ["did:3:bafyserviceProvider..."],
  "content": {
    "serviceEndpoint": "http://myservice.com/api/v0/",
    "serviceFunctions": [{
      "name": "requestTimestamp",
      "parameterSchema": ...,
      "resultSchema": ...
    }, {
      "name": "resolveTimestamp",
      "parameterSchema": ...,
      "resultSchema": ...
    }]
  }
}
```

### Privacy Policy

A **Privacy Policy tile** allows the user to specify their database instances in a specific *collection* and set special access control rights for these databases. In it the *source* of the data, i.e. the **Service Policy tile** that provides a hosting service, is set which contains the information about where data is backed up. A user can of course set multiple *sources* for a given *collection* for redundant backups, or even change where data is being hosted.

If the user wants to remove some database this can also be done in the **Privacy Policy tile** which contains the history of all databases that have been created and removed.

**Example:**

```JSON
{
  "doctype": "tile",
  "owners": ["did:3:bafyuser..."],
  "content": {
    "collection": "/ceramic/bafy0...", // Ceramic id of Collection tile
    "sources": ["/ceramic/bafy1..."], // Ceramic id of Service tile
    "databases": [{
      "orbitdb": "/orbitdb/kvstore/Qmfeiojsdf...",
      "accessList": [{
       "did": "did:3:user2...",
       "permissions": "read"
     }]
    }, {
      "orbitdb": "/orbitdb/feedstore/Qmfeiojfew..."
    }, {
      "orbitdb": "/orbitdb/feedstore/Qmh3249ghd...",
      "deleted": true
    }]
  }
}
```
