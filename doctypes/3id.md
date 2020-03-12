# 3id doctype

> Self-sovereign identities using the DID standard

3ID is a DID method that uses Ceramic to create, update, and resolve DID documents. The [DID standard](https://w3c.github.io/did-core) provides a general format for verifiable decentralized digital identities. Given a specific DID string, a DID document which contains public keys, services, authentication authorization, etc. can be resolved.

## Index

- [Identifier](#identifier)
- [Genesis record](#genesis-record)
- [Signed record](#signed-record)
- [Update rules](#update-rules)
- [DID document](#did-document)
- [Example](#example)

## Identifier

**Doctype name:** `3id`

**Ceramic identifier:** `/ceramic/3id/<genesis-CID>`

The DID standard encodes DID strings differently from Ceramic identifiers. A 3ID is represented as `did:3:<genesis-CID>` in the DID standard.

## Genesis record

The genesis record should contain three public keys. `management`, `signing`, and `encryption`. All of these keys are encoded using [multicodec](https://github.com/multiformats/multicodec). The first two keys are used for signatures and use the secp256k1 curve. The encyrption key uses the x25519 curve. The `management` key is used for signing updates to the 3ID document. The `signing` key is the main authentication key of the DID, and the `encryption` key is used by others to encrypt messages to this DID. There can also be additional content in the 3ID genesis record, as long as the `management` key is set. The 3ID genesis records are stored in IPFS using the [`dag-cbor`](https://github.com/ipld/js-ipld-dag-cbor/) IPLD format.

    {
      "doctype": "3id",
      "content": {
        "publicKeys": {
          "management": <secp256k1-public-key>,
          "signing": <secp256k1-public-key>,
          "encryption": <x25519-public-key>
        }
      }
    }

For backwards compatibility reasons documents created with [js-ipfs-did-document](https://github.com/3box/js-ipfs-did-document/) can also be used as a 3ID genesis record, given that it has the `managementKey`, `signingKey`, and `encryptionKey` properties set.

## Signed record

Signed records in 3ID are stored in IPFS using the `dag-jose` or `dag-cose` formats. These formats allow for IPLD objects to be stored along with a standardized signature format. They also allow DAG objects to be encrypted. The update to the document is encoded using [json-patch](https://github.com/Starcounter-Jack/JSON-Patch).

    {
      "next": <CID-previous-record>,
      "content": <JSON-patch-object>
    }

## Update rules

All valid updates must be signed by the current `management` key. An update can change anything about the content of the document except remove the `management` key. It can however change the `management` key. This is called a *key rotation*. 

## DID document

The [DID specification](https://w3c.github.io/did-core/) describes what a DID document should look like. This format is a bit different than how the data is stored in the 3id ceramic document. Exactly how the conversion between the two will take place is yet to be determined.

## Example

The example below is the `content` of a typical 3ID document. The `collection` property contains *Collection Policies* as keys and *Privacy Policies* as values. The former is a description of a data set, usually created by an application, and the latter is the users own settings for this collection, such as specific database references and where they are hosted. In the `accountLinks` property any account-link documents that link blockchain accounts to this 3ID can be found.

    {
      "publicKeys": {
        "management": <secp256k1-public-key>,
        "signing": <secp256k1-public-key>,
        "encryption": <x25519-public-key>
      },
      "collections": {
        "/ceramic/policy/bafy11111...": "/ceramic/policy/bafyaaaaa...",
        "/ceramic/policy/bafy22222...": "/ceramic/policy/bafybbbbb..."
      },
      "accountLinks": [
        "/ceramic/account-link/bafy00000..."
      ]
    }


