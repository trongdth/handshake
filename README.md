![Alt text](logo.png?raw=true "Handshake")

-----------------

# Handshake (Crypto signature)

Handshake is implemented as an **Ethereum smart contract** written in [Solidity](https://github.com/ethereum/solidity).

For the basic handshakes that involves no payment, we provide the contract [BasicHandshake](contracts/BasicHandshake.sol) to facilitate an agreement between 2 parties.

For a more advance usecase which involves a payment (currently in ETH), [PayableHandshake](contracts/PayableHandshake.sol) receives an escrow and lock it for some specified time window. To protect users' payment, the contract only transfers the payment when both parties agree with each other.

## Basic handshake

Allows two entities making aggrement on anything they want. In order to have a handshake, the first entity will initiate the handshake and the second entity will shake.

#### API

---
```javascript
function init(address acceptor, bytes32 offchain) public
```

Offer a new handshake to *acceptor*. Additional data is stored offchain and is refered by the key *offchain*.
For some special usecase where *acceptor* is not known beforehand (e.g., a freelancer contract), acceptor can be set to `0`. This special handshake can be accepted by anyone later.

The function emits an event contains the id of the new handshake.

---
```javascript
function shake(uint hid, bytes32 offchain) public
```

Accept a handshake reqest from another user.
The handshake is referred by the id *hid* received from the event in `init`.

---
## Payable handshake

Allows two entities (payer & payee) making aggrement for a product or service which involves payment.

The payable handshakes operate as a state machine with 6 states: `Inited`, `Shaked`, `Accepted`, `Rejected`, `Done`, and `Cancelled`.
Initally, a new handshake is created with `Inited` state by a payee.

When a payer accepts that handshake and escrows ETH, the state changes to `Shaked`.

The payee works on their product and deliver it to the payer before `deadline` to change the state to `Accepted`. Otherwise, if the product is not delivered before `deadline`, the payer can cancel the handshake and get a full refund.

After the payee delivers the product, there's a time window of **7 days** for the payer to review it. If the payer doesn't reject the product, the payee can withdraw the payment. Otherwise, the payee needs to resolve the conflict with their payer in the next **14 days**. If the payer still rejects after this time frame, they can cancel the handshake and withdraw back their escrow.

#### API

---
```javascript
function init(address payer, uint value, uint deadline, bytes32 offchain) public
```
Payee proposes a handshake to a payer for a specified value and deadline.

---
```javascript
function initByPayer(address payee, uint value, uint deadline,bytes32 offchain) public payable
```
Payer initiates handshake with payee address, escrow ETH and set deadline.

---
```javascript
function shake(uint hid, bytes32 offchain) public payable
```
Payer accept a handshake and escrow enough ETH.

---
```javascript
function deliver(uint hid, bytes32 offchain) public
```
After deliver the product to payer, payee notify payer to start reviewing it.

---
```javascript
function withdraw(uint hid, bytes32 offchain) public
```
Payee withdraw the escrowd ETH after the work had been reviewed by payer.

---
```javascript
function reject(uint hid, bytes32 offchain) public
```
Payer rejects delivered product.

---
```javascript
function accept(uint hid, bytes32 offchain) public
```
Payer accepts the delivered product.

---
```javascript
function cancel(uint hid, bytes32 offchain) public
```
Payer cancels handshake and withdraw the escrowed ETH.

---
## Setup

##### Prerequisites

* Clone the repository.
* Install [truffle](https://github.com/trufflesuite/truffle) framework and [ganache-cli](https://github.com/trufflesuite/ganache-cli):

```bash
    npm install -g truffle ganache-cli
```

##### Compile contracts

## Test

To run the unit tests:
```bash
    truffle test "test/basicHandshake.js"
    truffle test "test/payableHandshake.js"
```

## Contributing

All contributions, feature requests and issue reports are welcomed. [Create an issue](https://github.com/cryptonomous/handshake/issues) to submit your requests or notify a bug. If you would like to add a feature, [make a pull request](https://github.com/cryptonomous/handshake/pulls).

## License
