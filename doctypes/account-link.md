# Account-link doctype

> Links from blockchain accounts to DIDs

Account links publicly associate a 3ID to other public identities, such as public keys, smart contracts, or other DIDs. Account links allow others to verify that two identities are owned by the same person or organization.

## Index

- [Genesis record](#genesis-record)
- [Signed record](#signed-record)
- [Update rules](#update-rules)
- [Examples](#examples)
  - [Ethereum account-link](#ethereum-account-link)
  - [Polkadot account-link](#polkadot-account-link)
  - [Cosmos account-link](#cosmos-account-link)
  - [Bitcoin account-link](#bitcoin-account-link)

## Genesis record

**Doctype name:** `account-link`

An account-link genesis record is very simple. It only contains the account id ([CAIP-10](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md) complaint) of the account that is being linked. In most cases this is a blockchain address, such as an ethereum, or bitcoin address. The account id is stored in the `owners` property as one string in an array.

Account-link genesis records are stored in IPFS using the [`dag-cbor`](https://github.com/ipld/js-ipld-dag-cbor/) IPLD format.

```JSON
{
  "doctype": "account-link",
  "owners": [<caip-10-account-id>]
}
```

## Signed record

The signed record of an account link might vary depending on what type of account it is. What these signed records have in common is some form of proof that the account has approved the given update contained in the record. The proof must include the DID that the account is being linked to as well as the CID contained in the `next` property. It should be very similar to the proof the [3id-blockchain-utils](https://github.com/3box/js-3id-blockchain-utils) library provides.

Account-link signed records are stored in IPFS using the [`dag-cbor`](https://github.com/ipld/js-ipld-dag-cbor/) IPLD format.

```JSON
{
  "next": <CID-previous-record>,
  "content": <link-proof>
}
```

## Update rules

An account link can only link from one account to one 3ID. Any update that violates this (and thus has an invalid proof) will not be accepted. All signatures need to be signed by the account specified in the `
` property. The `owners` property can not be changed.

## Examples

When resolving an account-link that is linked to a 3ID the result looks like the examples below.

### Ethereum account-link
```JSON
{
  "owners": ["0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb@eip155:1"],
  "content": "did:3:bafyreiecedg6ipyvwdwycdjiakhj5hiuuutxlvywtkvckwvsnu6pjbwxae"
}
```

### Polkadot account-link
```JSON
{
  "owners": ["5hmuyxw9xdgbpptgypokw4thfyoe3ryenebr381z9iaegmfy@polkadot:polkadot-chain"],
  "content": "did:3:bafyreiecedg6ipyvwdwycdjiakhj5hiuuutxlvywtkvckwvsnu6pjbwxae"
}
```

### Cosmos account-link
```JSON
{
  "owners": ["cosmos1t2uflqwqe0fsj0shcfkrvpukewcw40yjj6hdc0@cosmos:cosmoshub-3"],
  "content": "did:3:bafyreiecedg6ipyvwdwycdjiakhj5hiuuutxlvywtkvckwvsnu6pjbwxae"
}
```

### Bitcoin account-link
```JSON
{
  "owners": ["128Lkh3S7CkDTBZ8W7BbpsN3YYizJMp8p6@bip122:000000000019d6689c085ae165831e93"],
  "content": "did:3:bafyreiecedg6ipyvwdwycdjiakhj5hiuuutxlvywtkvckwvsnu6pjbwxae"
}
```
