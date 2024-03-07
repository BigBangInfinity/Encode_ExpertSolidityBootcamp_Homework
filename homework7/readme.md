# Homework 7
## Functions

1. The parameter X represents a function. Complete the function signature so that X is a standard ERC20 transfer function (other than the visibility). The query function should revert if the ERC20 function returns false.
  
  ```
  // SPDX-License-Identifier: MIT
  // Compatible with OpenZeppelin Contracts ^5.0.0
  pragma solidity ^0.8.20;
  
  import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
  import "@openzeppelin/contracts/access/Ownable.sol";
  
  import "hardhat/console.sol";
  
  contract MyToken is ERC20, Ownable {
      constructor()
          ERC20("MyToken", "MTK")
          Ownable(msg.sender)
      {
          _mint(msg.sender, 1000 * 10 ** decimals());
      }
  
      function mint(address to, uint256 amount) public onlyOwner {
          _mint(to, amount);
      }
  
      function query(uint256 _amount, address _receiver, function(address, address, uint256) external returns (bool)  callback) internal {
          require (callback(msg.sender, _receiver, _amount));
      }
  
      function reply(uint256 _amount, address _receiver) public  {
          query(_amount, _receiver, this.transferFrom);
      }
  
  }
  ```

  I tried to use `this.transfer`, but then when query is called, the code tries to sent tokens from the contract's address, not from msg.sender's address, so I had to use `this.transferFrom` instead.
