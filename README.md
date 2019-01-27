<h3 align="center">
  <br />
  <img src="https://user-images.githubusercontent.com/168240/39537094-61c30484-4ded-11e8-8c93-410ba0510cf4.png" alt="logo" width="750" />
  <br />
  <br />
  <br />
</h3>

# ethereum-input-data-decoder

> Ethereum smart contract transaction input data decoder

[![License](http://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/miguelmota/ethereum-input-data-decoder/master/LICENSE) [![Build Status](https://travis-ci.org/miguelmota/ethereum-input-data-decoder.svg?branch=master)](https://travis-ci.org/miguelmota/ethereum-input-data-decoder) [![dependencies Status](https://david-dm.org/miguelmota/ethereum-input-data-decoder/status.svg)](https://david-dm.org/miguelmota/ethereum-input-data-decoder) [![NPM version](https://badge.fury.io/js/ethereum-input-data-decoder.svg)](http://badge.fury.io/js/ethereum-input-data-decoder)

## Demo

[https://lab.miguelmota.com/ethereum-input-data-decoder](https://lab.miguelmota.com/ethereum-input-data-decoder)

## Install

```bash
npm install ethereum-input-data-decoder
```

## Getting started

Pass [ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) file path to decoder constructor:

```javascript
const InputDataDecoder = require('ethereum-input-data-decoder');
const decoder = new InputDataDecoder(`${__dirname}/abi.json`);
```

Alternatively, you can pass ABI array object to constructor;

```javascript
const abi = [{ ... }]
const decoder = new InputDataDecoder(abi);
```

[example abi](./test/abi.json)

Then you can decode input data:

```javascript
const data = `0x67043cae0000000000000000000000005a9dac9315fdd1c3d13ef8af7fdfeb522db08f020000000000000000000000000000000000000000000000000000000058a20230000000000000000000000000000000000000000000000000000000000040293400000000000000000000000000000000000000000000000000000000000000a0f3df64775a2dfb6bc9e09dced96d0816ff5055bf95da13ce5b6c3f53b97071c800000000000000000000000000000000000000000000000000000000000000034254430000000000000000000000000000000000000000000000000000000000`;

const result = decoder.decodeData(data);

console.log(result);
```

```text
{
  "name": "registerOffChainDonation",
  "types": [
    "address",
    "uint256",
    "uint256",
    "string",
    "bytes32"
    ],
    "inputs": [
      <BN: 5a9dac9315fdd1c3d13ef8af7fdfeb522db08f02>,
      <BN: 58a20230>,
      <BN: 402934>,
      "BTC",
      <Buffer f3 df ... 71 c8>
    ]
}
```

Example using input response from [web3.getTransaction](https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethgettransaction):

```javascript
web3.eth.getTransaction(txHash, (error, txResult) => {
  const result = decoder.decodeData(txResult.input);
  console.log(result);
});
```

### Decoding Big Numbers

All numbers are returned in [big number](https://github.com/indutny/bn.js) format to preserve precision.

Here's an example of how to convert the big number to a human readable format.

```js
console.log(result.inputs[0].toString(10)) // "5"
console.log(result.inputs[0].toNumber()) // 55
```

Please keep in mind that JavaScript only supports numbers up to 64 bits. Solidity numbers can be up to 256 bits, so you run the risk of truncation when casting or having the big number library error out when trying to parse a large number to a JavaScript Number type.

```js
const n = new BN("543534254523452352345234523455")
console.log(n.toString(10)) // "543534254523452352345234523455"
console.log(n.toNumber()) // ERROR!
```

## CLI

## Install

```bash
npm install -g ethereum-input-data-decoder
```

### Usage

```bash
$ ethereum_input_data_decoder --help

  Ethereum smart contract transaction input data decoder

  Usage
    $ ethereum_input_data_decoder [flags] [input]

  Options
    --abi, -a  ABI file path
    --input, -i Input data file path

  Examples
    $ ethereum_input_data_decoder --abi token.abi --input data.txt
    $ ethereum_input_data_decoder --abi token.abi "0x23b872dd..."
```

### Example

Pass ABI file and input data as file:

```bash
$ ethereum_input_data_decoder --abi abi.json --input data.tx

name      registerOffChainDonation
address   0x5a9dac9315fdd1c3d13ef8af7fdfeb522db08f02
uint256   1487012400
uint256   4204852
string    BTC
bytes32   0xf3df64775a2dfb6bc9e09dced96d0816ff5055bf95da13ce5b6c3f53b97071c8
```

Pass ABI file and input data as string:

```bash
$ ethereum_input_data_decoder --abi abi.json 0x67043cae0...000000

name      registerOffChainDonation
address   0x5a9dac9315fdd1c3d13ef8af7fdfeb522db08f02
uint256   1487012400
uint256   4204852
string    BTC
bytes32   0xf3df64775a2dfb6bc9e09dced96d0816ff5055bf95da13ce5b6c3f53b97071c8
```

You can also pipe the input data:

```bash
$ cat data.txt | ethereum_input_data_decoder --abi abi.json

name      registerOffChainDonation
address   0x5a9dac9315fdd1c3d13ef8af7fdfeb522db08f02
uint256   1487012400
uint256   4204852
string    BTC
bytes32   0xf3df64775a2dfb6bc9e09dced96d0816ff5055bf95da13ce5b6c3f53b97071c8
```

## Test

```bash
npm test
```

## Development

1. Clone repository:

  ```bash
  git clone git@github.com:miguelmota/ethereum-input-data-decoder.git

  cd ethereum-input-data-decoder/
  ```

2. Install dependencies:

  ```bash
  npm install
  ```

3. Make changes.

4. Run tests:

  ```bash
  npm test
  ```

5. Run linter:

  ```bash
  npm run lint:fix
  ```

## FAQ

- Q: How can I retrieve the ABI?

  - A: You can generate the ABI from the solidity source files using the [Solidity Compiler](http://solidity.readthedocs.io/en/develop/installing-solidity.html).

    ```bash
    solc --abi MyContract.sol -o build
    ```

- Q: Can this library decode contract creation input data?

  - A: Yes, it can decode contract creation input data.

## License

[MIT](LICENSE)
