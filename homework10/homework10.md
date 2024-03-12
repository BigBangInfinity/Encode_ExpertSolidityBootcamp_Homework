# Homework 10

## Audit 1
Imagine you have been given the following code to audit

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract DogCoinGame is ERC20 {
    uint256 public currentPrize;
    uint256 public numberPlayers;
    address payable[] public players;
    address payable[] public winners;

    event startPayout();

    constructor() ERC20("DogCoin", "DOG") {}

    function addPlayer(address payable _player) public payable {
        if (msg.value == 1) {
            players.push(_player);
        }
        numberPlayers++;
        if (numberPlayers > 200) {
            emit startPayout();
        }
    }

    function addWinner(address payable _winner) public {
        winners.push(_winner);
    }

    function payout() public {
        if (address(this).balance == 100) {
            uint256 amountToPay = winners.length / 100;
            payWinners(amountToPay);
        }
    }

    function payWinners(uint256 _amount) public {
        for (uint256 i = 0; i <= winners.length; i++) {
            winners[i].send(_amount);
        }
    }
}
```

with the following note from the team

_"DogCoinGame is a game where players are added to the contract via the addPlayer function, they need to send 1 ETH to play. Once 200 players have entered, the UI will be notified by the startPayout event, and will pick 100 winners which will be added to the winners
array, the UI will then call the payout function to pay each of the winners. The remaining balance will be kept as profit for the developers."_

Write out the main points that you would include in an audit report.

1. `currentPrize` variable is unused

2. `numberPlayers` does not need to be `uint256`

3. `players` array elements do not need to be  `payable`

4. check should be `if (msg.value == 1 ether)` and not `if (msg.value == 1)` because the latter would add the player if 1 wei has been sent to the contract, not 1 ETH. Also, numberPlayers should only increment if 1 ether was sent. The event `startPayout` only needs to be triggered once when numberPlayers reaches 200.

5. `addPlayer` function should be rewritten:

```
    function addPlayer(address payable _player) public payable {
        if (msg.value == 1 ether) {
            players.push(_player);
            numberPlayers++;
        }
        
        if (numberPlayers == 200) {
            emit startPayout();
        }
    }
```

I would also suggest to include to `revert` the transaction of the amount sent is not 1 ETH.

6. Only the owner should be able to call the functions `addWinner`, `payout`, `payWinners`. Best is to set the contract owner at initialization in the constructor.

7. The condition in the `payout` function should be `if (address(this).balance >= 100 ether)`. `amountToPay` should be multiplied by `1e18` otherwise the prize is in wei, not in ETH.

8. Best practice should be to use `winners[i].transfer(_amount);` instead of `winners[i].send(_amount);` because  `transfer` includes error handling unless a custom error handler is implemented for the  `send` function.

9. There should be also a function which allows the owner to withdraw ETH from the contract. A suggestion would be to separate ownerPool and prizePool, and the owner should only be able to withdraw from the ownerPool.
