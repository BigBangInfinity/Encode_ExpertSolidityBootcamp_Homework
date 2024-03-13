# Homework 11

## Auditing and Formal Methods

1. Audit for tokens
Go through the token [checklist](https://gist.github.com/shayanb/cd495e23c7cf1a8b269f8ce7fd198538) for this [contract](https://gist.github.com/extropyCoder/933b1815629baa873c4d7369e1fc9c76)

_Allowance: Double withdrawal (front-running)_

The `increaseAllowance` and `decreaseAllowance` functions mitigate the attack, but the `_approve` should only be called if the allowance is set to zero.

_decimals():	The decimals can be more than 18_

The contract has 18 decimals.

_Not accounting for the tokens that try to prevent multiple withdrawal attack_

No swap functionality in the contract, does not apply.

_Unprotected ‍‍‍‍‍‍‍transferFrom()_

Does not apply. Only applies if there is another smart contract which a spender is approved and that contract is vulnerable.

_External Calls:	Unchecked Call Return Value_

Does not apply. Contract contains no `.call` function.

_DoS with unexpected revert_

Does not apply, contract has no functionality of sending ETH.

_Transfers: Might return False instead of Revert_

internal `_transfer` function has `require` statements which would cause invalid transactions to revert.

_Missing return value_

`transfer` function returns `bool`.

_Internal Accounting discrepancy with the Actual Balance_

story deleted from website

_Blacklistable:	Blacklisted addresses cannot receive or send tokens_

Contract contains no blacklist.

_Mintable / Burnable:	TotalSupply can change by trusted actors_

The `_mint` function is internal and can be only called by the constructor. So the total supply cannot change.

_Pausable	All functionalities can be paused by trusted actors_

Contract is not pausable.
