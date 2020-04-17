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

```JSONC
{
  "doctype": "tile",
  "owners": ["did:3:<CID>"],
  "content": ...
}
```

When creating a new tile its _docId_ will be deterministically generated from the content, owners array, and the signature of the user. This means that if a document is being created that already been created the new updates will be automatically loaded. If this is not desired a random `nonce` can be added to the content which will make every _docId_ unique.

## Signed record

Signed records in 3ID are stored in IPFS using the `dag-jose` or `dag-cose` formats. These formats allow for IPLD objects to be stored along with a standardized signature format. They also allow DAG objects to be encrypted. The update to the document is encoded using [json-patch](https://github.com/Starcounter-Jack/JSON-Patch).

```JSONC
{
  "prev": <CID-previous-record>,
  "content": <JSON-patch-object>
}
```

## Update rules

An update to a tile can change anything in the `content` of the tile document. If there are multiple owners only the first _signed record_ needs to contain the json-patch that changes the document. The other owners can simply sign a record that only contains the `prev` pointer to the previous signed record.

## Examples

Below there is an example of a Service Policy. For more examples see the [User Account Model](../data-models/account.md).

### Service Policy

A **Service Policy tile** is used to describe a simple service, usually in the form of a function that takes an input and produces an output. But could also describe more complex services such as database hosting etc.

**Example:**

This is an example of how the service tile for the Ceramic anchoring service could look like.

```JSONC
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

