# Account-link doctype

> Links from blockchain addresses to DIDs

Account links publicly associate a 3ID to other public identities, such as public keys, smart contracts, or other DIDs. Account links allow others to verify that two identities are owned by the same person or organization.

## Index

- [Identifier](#identifier)
- [Genesis record](#genesis-record)
- [Signed record](#signed-record)
- [Update rules](#update-rules)
- [Examples](#examples)
  - [Ethereum account-link](#ethereum-account-link)
  - [Polkadot account-link](#polkadot-account-link)
  - [Cosmos account-link](#cosmos-account-link)
  - [Bitcoin account-link](#bitcoin-account-link)

## Identifier

**Doctype name:** `account-link`

**Ceramic identifier:** `/ceramic/account-link/<genesis-CID>`

## Genesis record

An account-link genesis record is very simple. It only contains the identifier of the account that is being linked. In most cases this is a blockchain address, such as an ethereum, or bitcoin address. In theory it could be any type of identifier that can be associated to a public key, or smart contract.

Account-link genesis records are stored in IPFS using the [`dag-cbor`](https://github.com/ipld/js-ipld-dag-cbor/) IPLD format.

```JSON
{
  "doctype": "account-link",
  "content": <account-identifier-string>
}
```

## Signed record

The signed record of an address link might vary depending on what type of account it is. What these signed records have in common is some form of proof that the account has approved the given update contained in the record. The proof must include the DID that the account is being linked to as well as the CID contained in the `next` property. It should be very similar to the proof the [3id-blockchain-utils](https://github.com/3box/js-3id-blockchain-utils) library provides.

Account-link signed records are stored in IPFS using the [`dag-cbor`](https://github.com/ipld/js-ipld-dag-cbor/) IPLD format.

```JSON
{
  "next": <CID-previous-record>,
  "content": <link-proof>
}
```

## Update rules

An account link can only link from one account to one 3ID. Any update that violates this (and thus has an invalid proof) will not be accepted.

## Examples

When resolving an account-link that is linked to a 3ID the result looks like the examples below.

### Ethereum account-link
```JSON
{
  "account": "0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
  "did": "did:3:bafyreiecedg6ipyvwdwycdjiakhj5hiuuutxlvywtkvckwvsnu6pjbwxae"
}
```

### Polkadot account-link
```JSON
{
  "account": "5hmuyxw9xdgbpptgypokw4thfyoe3ryenebr381z9iaegmfy",
  "did": "did:3:bafyreiecedg6ipyvwdwycdjiakhj5hiuuutxlvywtkvckwvsnu6pjbwxae"
}
```

### Cosmos account-link
```JSON
{
  "account": "cosmos1t2uflqwqe0fsj0shcfkrvpukewcw40yjj6hdc0",
  "did": "did:3:bafyreiecedg6ipyvwdwycdjiakhj5hiuuutxlvywtkvckwvsnu6pjbwxae"
}
```

### **Bitcoin account-link**
```JSON
{
  "account": "128Lkh3S7CkDTBZ8W7BbpsN3YYizJMp8p6",
  "did": "did:3:bafyreiecedg6ipyvwdwycdjiakhj5hiuuutxlvywtkvckwvsnu6pjbwxae"
}
```
