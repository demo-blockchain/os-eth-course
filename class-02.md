# Intro. Class 02
## Solidity: Programming for Ethereum
---
# Ethereum Components Lifecycle

## Transactions
1. Form a raw transaction:
```var rawTx = {
nonce = web3.toHex(0),
gasPrice = web3.toHex(20000000000),
gasLimit = web3.toHex(100000),
to: checkSummedAddress,
value: web3.toHex(1000),
data: ‘0xc0de’
};
```
2. Hash transaction using `keccack256`:  `hashedTx =new EthereumTx(rawTx)`
3. Sign transaction: `signedTx = hashedTx.sign(privKey)`

???
- https://medium.com/@codetractio/inside-an-ethereum-transaction-fa94ffca912f
- https://medium.com/blockchannel/life-cycle-of-an-ethereum-transaction-e5c66bae0f6e

## Deploying Smart Contracts (EVM)
1. Write Solidity contract
2. Deploy Solidity contract onto the network, and change the state of the EVM
  a. Compile contract
  a. Create new transaction
  a. Append compiled contract onto transaction
  a. Compile transaction
  a. Set gas price and limit
  a. Deploy and pay gas
3. Further calls to any of the contract’s functions will require gas as well if they change the state of the EVM

???
- https://ethereumdev.io/ethereum-smart-contracts-lifecycle/
- https://ethereum.stackexchange.com/questions/37073/when-i-deploy-a-smart-contract-does-it-create-a-new-block-or-when-i-perfrom-a-wr

## Interacting with DApps
- Serves as an interface to the smart contracts
  1. Interact with contracts through web3.js
  2. Connect with MetaMask or any other light client through RPC API (Infura, in MetaMask’s case)
  3. Pay gas to perform any actions using the API and your wallet
---
# What’s Solidity?
Solidity is the main programming language we use in order to create smart contract on the Ethereum network.

It lives on top of EVM (Ethereum Virtual Machine) meaning that it supports turing-complete features. 

`if...`
- ![](https://www.tutorialspoint.com/scala/images/scala_decision_making.jpg "if loop - courtesy of Tutorialspoint")

`while...`
- ![](https://www.tutorialspoint.com/cprogramming/images/cpp_while_loop.jpg "while loop - courtesy of Tutorialspoint")
---
# How do we ensure correct execution?
In order to prevent from infinite loops and never ending code executions of any sort to halt the network, Ethereum have introduced a new concept called gas.
Gas basically acts as a time out mechanism and allows the contracts to run according to the amount of gas paid, when contract was deployed.
---
# Syntax
## How do we write Solidity?
---
## Prsentation Structure
- File Layout
- Structure of Contract
  - State Variables (bool, uint, String, address)
  - Structs
  - Enum
  - Modifier guards
  - Events
---
# File Layout
## Versioning
`pragma solidity ^0.4.0;`
- `pragma` means we're about to make pragmatic statement relating to the version used
- `^<version>` means until a `<version>` with breaking changes
- `solidity` is the language we write the contract in
Versioning pragma
---
# File Layout
- `import * as symbolName from "filename”;`
## Import statements
- `symbolName` is the of the variable we want to import the contract into
The file we actually import from, specify path here
## Alternative form:
- `import from "filename”;`
---
# File Layout
## Contract’s statement and comments
```contract <contractName> {
// Contract content goes here
/*
Contract comments can
Be spread among many lines
*/
}
```
---
# Structure of contract

## State variables types
- bool
- uint / int
- string
- address
  
## Defining data structures
- Enum
- Struct
- mapping
  
## Function helpers
- modifier
- Event
  
# More functions to consider
- Error Handling predefined functions
- Mathematical and Cryptographic Functions
  - Hashing
  - Elliptic curve operations
---
# State variables types
## bool
`bool`: The possible values are constants true and false.
`Operators:
- ! (logical negation)
- && (logical conjunction, “and”)
- || (logical disjunction, “or”)
- == (equality)
- != (inequality)

???
- The operators `||` and `&&` apply the common short-circuiting rules. Meaning that in the expression `f(x) || g(y)`:
  - if `f(x)` evaluates to true, `g(y)` will not be evaluated even if it may have side-effects.
---
# State variables types
## int / uint
`int / uint`: Signed and unsigned integers of various sizes. Keywords uint8 to uint256 in steps of 8 (unsigned of 8 up to 256 bits) and int8 to int256. uint and int are aliases for uint256and int256, respectively.
## Operators:
- Comparisons:
  - `<=, <, ==, !=, >=, >` (evaluate to bool)
- Bit operators: 
  - `&, |, ^` (bitwise exclusive or)
  - `~` (bitwise negation)
- Arithmetic operators:
  -  Standard: `+, -, *, /`
  -  Advanced: `% , **, << , >>`
  -  Unary: `unary -, unary +`  
Division by zero and modulus with zero throws a runtime exception.
---
# State variables types
## String
- `bytes`:
Dynamically-sized byte array, see `Arrays`. Not a value-type!
- `string`:
Dynamically-sized UTF-8-encoded string, see `Arrays`. Not a value-type!
---
# State variables types
## address 
address: Holds a 20 byte value (size of an Ethereum address). Address types also have members and serve as a base for all contracts.
## Operators:
- `<=, <, ==, !=, >= and >` -- Standard as exaplained before
- `balance() and transfer()`
  - For a quick reference, see `Address Related`.
  It is possible to query the balance of an address using the property balance and to send Ether (in units of wei) to an address using the transfer function:
- `send()`
  - Send is the low-level counterpart of transfer. If the execution fails, the current contract will not stop with an exception, but send will return false.
- `call(), callcode() and delegatecall()`

???
- Side notes:
Furthermore, to interface with contracts that do not adhere to the ABI, the function call is provided which takes an arbitrary number of arguments of any type. These arguments are padded to 32 bytes and concatenated. One exception is the case where the first argument is encoded to exactly four bytes. In this case, it is not padded to allow the use of function signatures here.
---
# Defining data structures
## Enums
Enums are one way to create a user-defined type in Solidity. They are explicitly convertible to and from all integer types but implicit conversion is not allowed. The explicit conversions check the value ranges at runtime and a failure causes an exception. Enums needs at least one member.
```pragma solidity ^0.4.16;
contract test {
  enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill }
  ActionChoices choice;
  ActionChoices constant defaultChoice = ActionChoices.GoStraight;
  function setGoStraight() public {
    choice = ActionChoices.GoStraight;
  }
  // Since enum types are not part of the ABI, the signature of "getChoice"
  // will automatically be changed to "getChoice() returns (uint8)"
  // for all matters external to Solidity. The integer type used is just
  // large enough to hold all enum values, i.e. if you have more values,
  // `uint16` will be used and so on.
  function getChoice() public view returns (ActionChoices) {
    return choice;
  }
  function getDefaultChoice() public pure returns (uint) {
    return uint(defaultChoice);
  }
}
```
---
# Defining data structures
## struct
Usual form of structs, it is basically a way to define new types. 
## Example
```
pragma solidity ^0.4.11;
contract CollegeChain{
struct Student {
  uint studentId;
  uint year;
  address studentAccount;
  string firstName;
  string lastName;
}
uint numStudents;
mapping (uint => Student) students;
function newStudent(address account, string firstName, string lastName, uint year) {
  studentID = numStudents++;
  students[studentID] = Student(year, account, firstName, lastName);
}}
```
---
# Defining data structures
## mapping
Mapping types are declared as mapping(_KeyType => _ValueType). Here _KeyType can be almost any type except for a mapping, a dynamically sized array, a contract, an enum and a struct. _ValueType can actually be any type, including mappings.
Mappings can be seen as hash tables which are virtually initialized such that every possible key exists and is mapped to a value whose byte-representation is all zeros
## Example
```
pragma solidity ^ 0.4 .11;
contract MappingExample {
  mapping(address => uint) public balances;

  function update(uint newBalance) public {
    balances[msg.sender] = newBalance;
  }
}
contract MappingUser {
  function f() public returns(uint) {
    MappingExample m = new MappingExample();
    m.update(100);
    return m.balances(this);
  }
}
```
---
# Function types & helpers: Function types
## Function types
`function (<parameter types>) {internal|external} [pure|constant|view|payable] [returns (<return types>)]`
---
# Function types & helpers: Modifiers
## Modifiers
Function modifiers can be used to amend the semantics of functions in a declarative way (see Function Modifiers in contracts section).
## Example
```
pragma solidity ^ 0.4 .11;
contract Purchase {
  address public seller;
  modifier onlySeller() { 
    // Modifier
    require(msg.sender == seller);
    _;
  }
  function abort() public onlySeller { 
    // Modifier usage
    // ...
  }
}
```
---
# Function types & helpers: Events
## Events
Events are convenience interfaces with the EVM logging facilities.
## Example
```
pragma solidity ^ 0.4 .21;
contract SimpleAuction {
  event HighestBidIncreased(address bidder, uint amount); 
  // Event
  function bid() public payable {
    // ...
    emit HighestBidIncreased(msg.sender, msg.value);
    // Triggering event
  }
}
```
---
# Units and Globally Available Variables
## Ether Units
A literal number can take a suffix of wei, finney, szabo or ether to convert between the subdenominations of Ether, where Ether currency numbers without a postfix are assumed to be Wei, e.g. 2 ether == 2000 finney evaluates to true.
---
# Units and Globally Available Variables
## Time Units
Suffixes like seconds, minutes, hours, days, weeks and years after literal numbers can be used to convert between units of time where seconds are the base unit and units are considered naively in the following way:
- 1 == 1 seconds
- 1 minutes == 60 seconds
- 1 hours == 60 minutes
- 1 days == 24 hours
- 1 weeks == 7 days
- 1 years == 365 days


???
- Be careful if you perform calendar calculations using these units, because not every year equals 365 days and not even every day has 24 hours because of leap seconds. Due to the fact that leap seconds cannot be predicted, an exact calendar library has to be updated by an external oracle.
These suffixes cannot be applied to variables. If you want to interpret some input variable in e.g. days, you can do it in the following way:
```function f(uint start, uint daysAfter) public {    if (now >= start + daysAfter * 1 days) {      // ...    }}```
---
# Units and Globally Available Variables
## Special Variables and Functions
There are special variables and functions which always exist in the global namespace and are mainly used to provide information about the blockchain.
## Block and Transaction Properties
- `block.blockhash(uint blockNumber) returns (bytes32)`: hash of the given block - only works for 256 most recent blocks excluding current
- `block.coinbase (address)`: current block miner’s address
- `block.difficulty (uint)`: current block difficulty
- `block.gaslimit (uint)`: current block gaslimit
- `block.number (uint)`: current block number
- `block.timestamp (uint)`: current block timestamp as seconds since unix epoch
- `gasleft() returns (uint256)`: remaining gas
- `msg.data (bytes)`: complete calldata
- `msg.gas (uint)`: remaining gas - deprecated in version 0.4.21 and to be replaced by `gasleft()`
- `msg.sender (address)`: sender of the message (current call)
- `msg.sig (bytes4)`: first four bytes of the calldata (i.e. function identifier)
- `msg.value (uint)`: number of wei sent with the message
- `now (uint)`: current block timestamp (alias for block.timestamp)
- `tx.gasprice (uint)`: gas price of the transaction
- `tx.origin (address)`: sender of the transaction (full call chain)
---
# Special Variables and Functions
## Block and Transaction Properties
- `block.blockhash(uint blockNumber) returns (bytes32)`: hash of the given block - only works for 256 most recent blocks excluding current
- `block.coinbase (address)`: current block miner’s address
- `block.difficulty (uint)`: current block difficulty
- `block.gaslimit (uint)`: current block gaslimit
- `block.number (uint)`: current block number
- `block.timestamp (uint)`: current block timestamp as seconds since unix epoch
- `gasleft() returns (uint256)`: remaining gas
- `msg.data (bytes)`: complete calldata
- `msg.gas (uint)`: remaining gas - deprecated in version 0.4.21 and to be replaced by `gasleft()`
- `msg.sender (address)`: sender of the message (current call)
- `msg.sig (bytes4)`: first four bytes of the calldata (i.e. function identifier)
- `msg.value (uint)`: number of wei sent with the message
- `now (uint)`: current block timestamp (alias for block.timestamp)
- `tx.gasprice (uint)`: gas price of the transaction
- `tx.origin (address)`: sender of the transaction (full call chain)

Do not rely on block.timestamp, now and block.blockhash as a source of randomness, unless you know what you are doing.
---
# Special Variables and Functions
## Error Handling
- `assert(bool condition)`:
throws if the condition is not met - to be used for internal errors.
- `require(bool condition)`:
throws if the condition is not met - to be used for errors in inputs or external components.
- `revert()`:
abort execution and revert state changes

Address Related
- `<address>.balance (uint256)`:
balance of the Address in Wei
- `<address>.transfer(uint256 amount)`:
send given amount of Wei to Address, throws on failure, forwards 2300 gas stipend, not adjustable
- `<address>.send(uint256 amount) returns (bool)`:
send given amount of Wei to Address, returns false on failure, forwards 2300 gas stipend, not adjustable
- `<address>.call(...) returns (bool)`:
issue low-level CALL, returns false on failure, forwards all available gas, adjustable
- `<address>.callcode(...) returns (bool)`:   Deprecated and discouraged
issue low-level CALLCODE, returns false on failure, forwards all available gas, adjustable
- `<address>.delegatecall(...) returns (bool)`:
issue low-level DELEGATECALL, returns false on failure, forwards all available gas, adjustable
---
# Special Variables and Functions
## Mathematical and Cryptographic Functions

- `addmod(uint x, uint y, uint k) returns (uint)`:
compute `(x + y) % k` where the addition is performed with arbitrary precision and does not wrap around at `2**256`. Assert that k != 0starting from version 0.5.0.
- `mulmod(uint x, uint y, uint k) returns (uint)`:
compute `(x * y) % k` where the multiplication is performed with arbitrary precision and does not wrap around at `2**256`. Assert that k != 0 starting from version 0.5.0.
- `keccak256(...) returns (bytes32)`:
compute the Ethereum-SHA-3 (Keccak-256) hash of the (tightly packed) arguments
- `sha256(...) returns (bytes32)`:
compute the SHA-256 hash of the (tightly packed) arguments
- `sha3(...) returns (bytes32)`:
alias to keccak256
- `ripemd160(...) returns (bytes20)`:
compute RIPEMD-160 hash of the (tightly packed) arguments
- `ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)`:
recover the address associated with the public key from elliptic curve signature or return zero on error (example usage)
