# Policy doctype

> Policy documents used to describe services, access control, etc.

Policies are a flexible way to create single or multi-party statements and can be used to define infrastructure descriptions, service agreements, routing information, data schema definitions, access control permissions, and much more.

## Index

- [Identifier](#identifier)
- [Genesis record](#genesis-record)
- [Signed record](#signed-record)
- [Update rules](#update-rules)
- [Examples](#examples)
    - [Collection policy](#collection-policy)
    - [Service policy](#service-policy)
    - [Privacy policy](#privacy-policy)

## Identifier

**Doctype name:** `policy`

**Ceramic identifier:** `/ceramic/policy/<genesis-CID>`

## Genesis record

The genesis record of a policy document is quite flexible. The only property that is required within the `content` is the `owners` property which must contain an array of DIDs. Other than that the record may contain data in any form. The `owners` array specifies who is allowed to update the document (note that a signature from all the owners is needed in order to make a valid update to a policy). In the example below the 3ID DID method is used, but Ceramic can support other DID methods as well.

Policy genesis records are stored in IPFS using the [`dag-cbor`](https://github.com/ipld/js-ipld-dag-cbor/) IPLD format.

```JSON
{
  "doctype": "policy",
  "content": {
    "owners": ["did:3:<CID>"]
  }
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

An update to a policy can change anything in the `content` of the policy document, except for the `owners` property. If there are multiple owners only the first *signed record* needs to contain the json-patch that changes the document. The other owners can simply sign a record that only contains the `next` pointer to the previous signed record.

## Examples

Below there are three examples of possible policies, Collection, Service, and Privacy policies. These policies in particular are useful when specifying a user cenrtic data storage system.

### Collection policy

The **Collection policy** represents a *collection* of data, or rather a *collection* of databases that has specific schemas. This policy also contain tags which describe what type of data is being stored within the collection.

**Example:**

```JSON
{
  "doctype": "policy",
  "content": {
    "owners": ["did:3:bafyappDeveloper..."],
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

### Service policy

A **Service policy** is used to describe a simple service, usually in the form of a function that takes an input and produces an output. But could also describe more complex services such as database hosting etc.

**Example:**

This is an example of how the service policy for the Ceramic anchoring service could look like.

```JSON
{
  "doctype": "policy",
  "content": {
    "owners": ["did:3:bafyserviceProvider..."],
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

### Privacy policy

A **Privacy policy** allows the user to specify their database instances in a specific *collection* and set special access control rights for these databases. In it the *source* of the data, i.e. the **Hosting policy**, is set which contains the information about where data is backed up. A user can of course set multiple *sources* for a given *collection* for redundant backups, or even change where data is being hosted.

If the user wants to remove some database this can also be done in the **Privacy policy** which contains the history of all databases that have been created and removed.

**Example:**

```JSON
{
  "doctype": "policy",
  "content": {
    "owners": ["did:3:bafyuser..."],
    "collection": "/ceramic/policy/bafy0...", // Ceramic id of Collection policy
    "sources": ["/ceramic/policy/bafy1..."], // Ceramic id of Service policy
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
