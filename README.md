# zencrypt

Simple and strong encryption using WebCrypto under the hood

## Table of contents

- [Usage](#usage)
- [API](#api)
  - [encrypt](#encrypt)
  - [decrypt](#decrypt)
  - [generateSecret](#generatesecret)
  - [hash](#hash)

## Usage

This package is distributed as ESM only, it polyfills the webcrypto module into global if not already defined.

```javascript
import { decrypt, encrypt } from "zencrypt";

const SECRET = "MY_SPECIAL_KEY";

const test = async value => {
  const encrypted = await encrypt(value, SECRET);

  console.log("encrypted: ", encrypted);

  const decrypted = await decrypt(encrypted, SECRET);

  console.log("decrypted: ", decrypted);
};

test("my secret value");
// encrypted: 4439AA90B8374AA440BE3F816ADF0EA3501B61DE699264D5CAD89C381E595DD2337DCD00E7AE33F37A87C38CED
// decrypted: my secret value
```

## API

#### encrypt

_encrypt(value: any, secret: any[, options: Object]): string_

Encrypt a `value` based on the passed `secret` and `options`. The `value` can be any serializable value, as can the `secret` (if not a string, under the hood they will be stringified).

```javascript
const SECRET = {some: 'special key'};

const result = await encrypt({some: 'data'}, SECRET);

console.log(result);
// FD3740F30AEF1B25588C7227+96800272F06DCD73749802EB6FF7052614C876D30ED7398B4579295CE90FC8
```

`options` is an object of the following possible values:

```javascript
{
  // charset to use when encoding / decoding text
  charset: string = 'utf-8',
  // size of the TypedArray used for the crypto iv
  ivSize: number = 12,
  // the parser used to deserialize the encrypted value
  parse: function = JSON.parse,
  // the stringifier used to serialize the secret / value to encrypt
  stringify: function = JSON.stringify,
}
```

#### decrypt

_decrypt(encryptedValue: string, secret: any[, options: Object]): string_

Decrypt the `encryptedValue` based on the passed `secret` and `options`. The `secret` must be equal in value as the one used in the `encrypt` method.

```javascript
const SECRET = {some: 'special key'};

const encrypted = await encrypt({some: 'data'}, SECRET);

console.log(encrypted);
// FD3740F30AEF1B25588C722796800272F06DCD73749802EB6FF7052614C876D30ED7398B4579295CE90FC8

const result = await decrypt(encrypted, SECRET);

console.log(result);
// {some: 'data'}
```

`options` is an object of the following possible values:

```javascript
{
  // charset to use when encoding / decoding text
  charset: string = 'utf-8',
  // size of the TypedArray used for the crypto iv
  ivSize: number = 12,
  // the parser used to deserialize the encrypted value
  parse: function = JSON.parse,
  // the stringifier used to serialize the secret
  stringify: function = JSON.stringify,
}
```

#### generateSecret

_generateSecret([options: Object]): CryptoKey_

Generate a dynamic secret to use in `encrypt` or `decrypt`.

```javascript
const SECRET = await generateSecret();

console.log(SECRET);
// CryptoKey {...}
```

`options` is an object of the following possible values:

```javascript
{
  // the length of the key used when generating a key
  keyLength: number = 256,
}
```

#### hash

_hash(value: any[, algorithm: string[, options: Object]]): string_

Generate a unique SHA-based one-way hash.

```javascript
const hashed = await hash('foo', 'SHA-1');

console.log(hashed);
// 0beec7b5ea3f0fdbc95d0dd47f3c5bc275da8a33
```

`algorithm` is a string of the following possible values:

- `SHA-1`
- `SHA-256` (default)
- `SHA-384`
- `SHA-512`

`options` is an object of the following possible values:

```javascript
{
  // charset to use when encoding / decoding text
  charset: string = 'utf-8',
  // the stringifier used to serialize the value to hash
  stringify: function = JSON.stringify,
}
```
