<p align="center">
  <img width="300px" src="https://i.imgur.com/bFVR9cD.jpg">
</p>


# Concatenate encrypted content from the blockchain

This document describes the `e-cat` (pronounced `E cat`) protocol. For contributions, look into `CONTRIBUTIONS.md`.

This protocol depends strongly on [`bcat`](https://bcat.bico.media/) protocol, which was introduced to offer a solution for putting files larger than the standard transaction size limit in BitcoinSV. The `bcat` protocol itself, relies on [`B://`](https://b.bitdb.network/), which is a format for constructing transactions that will contain any type of content, (especially Raw Binary data) and is considered the standard for constructing OP_RETURN transactions so that different parties will be able to deconstruct and read the content of those transactions. 


## Abstract
The `e-cat` protocol combines cryptographic methods to put all types of data on the blockchain. The data is *Encrypted* in a way where only parties with pre-defined authorizations will be able to decrypt and concatenate the data from the blockchain. The protocol also offers a way of sharing these encrypted data and defining new access rules, without the need to upload and decrypt/re-encrypt the whole file again.


## Description
We have changed the principles of `B://` and `bcat` for e-cat and have defined some new ones The way that we are constructing the `B` parts, is almost identical to the original protocol, with one little exception, we are using our own namespace instead of `bitdb`'s. Our namespace for `B` parts, is `ecat::EPT`. So the exact specifications of constructing a `B` transaction is as follows:

1. `ECAT` part namespace: `ecat:EPT`
2. `(OPTIONAL)` Order (index) of this part in the list of file chunks
3. Raw data representing a part of the original file

## File Encryption
We are using AES symmetric method to encrypt the file, and therefore we need to generate a `secret key`. After encrypting the file's content, we will split the encrypted payload to properly sized chunks and construct the `B` transactions following the specifications described above.

```
1. A unique `secret key` will get generated
2. The file will get encrypted, using the same `secret key` in a symmetric way
```

## Creating `decryption key`s
One of the changes that we have made to the `bcat` protocol, is we have defined something called an `decryption key`. These `decryption key`s are basically, an *Encrypted* version of the `secret key` used to encrypt the file in the previous step.

We have defined two different parties in our protocol, one is the **Owner** of the file and the other is **ecat.media**. The `e-cat` protocol, enables both parties to access (decrypt) 
the file from the blockchain.

The method that we have used to prevent any unauthorized access to the file content, and give the authorized parties the ability of decrypting the file content, is to create `decryption key`s using authorized parties' `Public Key`s, so they would be able to use their `Private Key` and find the `secret key` required to decrypt the file.

```
3. The generated `secret key` will get encrypted twice, in an asymmetric way:
    * `secret key` + `your public key` (I)
    * `secret key` + `ecat's public key` (II)
4. We will call those two payloads, which were generated in the previous step, `encryption keys`
```

## Linker Transaction
We are following the main idea behind the `bcat` protocol, and below, we have described the process of creating different parts of this transaction.

After crafting `encryption keys`, we will be able to create our final `bcat` (linker) transaction.

These are the specifications for creating a `linker` transaction:

1. `ECAT` linker namespace: `ecat:ECT`
2. `'type'` literal string
3. String providing file MIME type, e.g. `image/jpeg`
4. Hexadecimal `Space` character: `0x20`
5. `'name'` literal string
6. String providing file name, e.g. `ecat_picture_sample`
7. Hexadecimal `Space` character: `0x20`
8. `encryption key` related to user/owner (I)
9. `'okey'` literal string
10. `encryption key` related to ecat (II)
11. `'eckey'` literal string
12. 32 bytes representing the bytes of the hash of the transaction with the `first` part of data of the file
13. 32 bytes representing the bytes of the hash of the transaction with the `second` part of data of the file
14. ...

```
5. We will put those `encryption keys` inside the `bcat` transaction that we are going to construct, next
```


****
## Contact
[mailto:ecat.media@protonmail.com](mailto:ecat.media@protonmail.com)

[@ecatdotmedia](https://twitter.com/ecatdotmedia)

[ecat.media](https://p.ecat.media/)
