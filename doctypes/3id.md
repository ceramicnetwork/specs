# 3id doctype

> Self-sovereign identities using the DID standard

3ID is a DID method that uses Ceramic to create, update, and resolve DID documents. The [DID standard](https://w3c.github.io/did-core) provides a general format for verifiable decentralized digital identities. Given a specific DID string, a DID document which contains public keys, services, authentication authorization, etc. can be resolved.

## Index

- [Genesis record](#genesis-record)
- [Signed record](#signed-record)
- [Update rules](#update-rules)
- [DID method](#did-document)
  - [Identifier](#identifier)
  - [DID document](#did-document)
- [Example](#example)

## Genesis record

**Doctype name:** `3id`

The genesis record should contain three public keys. The first one is the management key and is stored in the `owners` array, a `signing`, and an `encryption` key. All of these keys are encoded using [multicodec](https://github.com/multiformats/multicodec). The management and signing keys are used for signatures and use the secp256k1 curve. The encyrption key uses the x25519 curve. The management / owner key is used for signing updates to the 3ID document. The `signing` key is the main authentication key of the DID, and the `encryption` key is used by others to encrypt messages to this DID. There can also be additional content in the 3ID genesis record. The 3ID genesis records are stored in IPFS using the [`dag-cbor`](https://github.com/ipld/js-ipld-dag-cbor/) IPLD format.

```JSONC
{
  "doctype": "3id",
  "owners": [<secp256k1-public-key>],
  "content": {
    "publicKeys": {
      "signing": <secp256k1-public-key>,
      "encryption": <x25519-public-key>
    }
  }
}
```

For backwards compatibility reasons documents created with [js-ipfs-did-document](https://github.com/3box/js-ipfs-did-document/) can also be used as a 3ID genesis record, given that it has the `managementKey`, `signingKey`, and `encryptionKey` properties set. The `managementKey` should be mapped to the `owners` array, `signingKey` and `encryptionKey` should be mapped to `signing`, and an `encryption` respectively.

## Signed record

Signed records in 3ID are stored in IPFS using the `dag-jose` or `dag-cose` formats. These formats allow for IPLD objects to be stored along with a standardized signature format. They also allow DAG objects to be encrypted. The update to the document is encoded using [json-patch](https://github.com/Starcounter-Jack/JSON-Patch).

```JSONC
{
  "next": <CID-previous-record>,
  "content": <JSON-patch-object>
}
```

## Update rules

All valid updates must be signed by the current management key as listed in the `owners` array. An update can change anything about the content of the document. It can also change the management key in the `owners` array. This is called a _key rotation_.

## DID method

### Identifier

The DID standard encodes DID strings differently from Ceramic identifiers. A 3ID is represented as `did:3:<genesis-CID>` in the DID standard.

### DID document

The [DID specification](https://w3c.github.io/did-core/) describes what a DID document should look like. This format is a bit different than how the data is stored in the 3id ceramic document. Exactly how the conversion between the two will take place is yet to be determined.

## Example

The example below is the `content` of a typical 3ID document. The `collection` property contains an array of [Collection Policy Tile](./doctypes/tile.md#collection-policy) and [Privacy Policy Tile](./doctypes/tile.md#privacy-policy). The former is a description of a data set, usually created by an application, and the latter is the users own settings for a collection, such as specific database references and where they are hosted. In the `accountLinks` property any account-link documents that link blockchain accounts to this 3ID can be found.

```JSONC
{
  "publicKeys": {
    "signing": <secp256k1-public-key>,
    "encryption": <x25519-public-key>
  },
  "collections": [{
    "collection": "/ceramic/bafy11111...",
    "privacy": "/ceramic/bafyaaaaa..."
   }, {
    "collection": "/ceramic/bafy22222..."
    "privacy": "/ceramic/bafybbbbb..."
  }],
  "accountLinks": [
    "/ceramic/bafy00000..."
  ]
}
```
