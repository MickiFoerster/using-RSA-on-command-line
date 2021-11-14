# Using RSA on Command-Line

## Abstract
Let's assume you want to send sensible data to someone else but you don't have
installed suitable software to send this data encrypted except `openssl`.
Then you can use openssl RSA funtionality to send the data encrypted.

## Details

Alice wants to send sensible data to Bob.

### Bob must generate a RSA keypair

```
openssl genrsa -out rsa.key 8192
openssl rsa -in rsa.key -pubout -outform PEM -out rsa.pub
```

### Bob sends public key to Alice

### Alice creates random key and encrypts the sensible file

```
openssl rand  -out random-key.bin 128
openssl enc -aes-256-ctr -salt -in topsecret.pdf -out topsecret.pdf.enc -pass file:./random-key.bin -pbkdf2
```

### Alice encrypts the random key per RSA

```
openssl rsautl -encrypt -inkey rsa.pub -pubin -in random-key.bin -out random-key.bin.rsa
```

## (Optional) Alice could sign the two files (Bob needs public key of Alice)

## Alice sends two encrypted files to Bob

```
send random-key.bin.rsa topsecret.pdf.enc alice@example.com
```

## Bob decrypts data

```
openssl rsautl -decrypt -inkey rsa.key -in random-key.bin.rsa  -out alice-random-key.bin
openssl enc -d -aes-256-ctr -in topsecret.pdf.enc -out alice-topsecret.pdf -pass file:./alice-random-key.bin -pbkdf2
```
