# Homework 6
## Assembly 2

1. Create a Solidity contract with one function
  The solidity function should return the amount of ETH that was passed to it, and the function
  body should be written in assembly
  ```
  // SPDX-License-Identifier: MIT
  pragma solidity ^0.8.0;
  
  contract EthAmountGetter {
      function getEthAmount() external payable returns (uint256) {
          uint256 ethAmount;
  
          assembly {
              // Retrieve the value (amount of ETH) sent with the transaction
              ethAmount := callvalue()
          }
  
          return ethAmount;
      }
  }
  ```

2. Do you know what this code is doing ?

   ```
   push9 0x601e8060093d393df3
   msize
   mstore # mem = 000...000 601e8060093d393df3
   # = 000...000 spawned constructor payload
   # copy the runtime bytecode after the constructor code in mem
   codesize # cs
   returndatasize # 0 cs
   msize # 0x20 0 cs
   codecopy # mem = 000...000 601e8060093d393df3 RUNTIME_BYTECODE
   # --- stack ---
   push1 9 # 9
   codesize # cs 9
   add # cs+9 = CS = total codesize in memory
   push1 23 # 23 CS
   returndatasize # 0 23 CS
   dup3 # CS 0 23 CS
   dup3 # 23 CS 0 23 CS
   callvalue # v 23 CS 0 23 CS
   create # addr1 0 23 CS
   pop # 0 23 CS
   create # addr2
   selfdestruct
   ```

   The runtime bytecode for this contract is `0x68601e8060093d393df35952383d59396009380160173d828234f050f0ff`

   First, the constructor is called (`0x601ethe8060093d393df3` could encode inputs) and `0x601ethe8060093d393df3` is stored to memory. 
   Then the runtime bytecode is stored to memory.
   Then two child contracts are called with the `CREATE` opcode. The `CREATE` opcode takes three inputs: `value`, `offset`, `size`. The part in the memory starting at `offset=23` with `size=CS` is exactly the constructor + runtime bytecode concatenated without the leading zeros.
   The `value` for the first child contract is the `callvalue`, i.e. the ETH sent at deployment to the contract. The value for the second child contract is `0`.
