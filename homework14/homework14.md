# Homework 14
## Solidity / Yul bitwise

1. What are the potential dangers when performing the following bitwise operations
  
    1. Left shift
    
    2. Right shift

2. Bit Operations

Imagine you have a uint256 variable in storage named x
check if x starts with `de` or `be`
if x starts with `0xde` multiply x by 4
if x starts with `0xbe` divide x by 4

Write the code in

a. Solidity

b. Yul

Which one is most gas efficient ?

To help you test your solution, [here](https://gist.github.com/extropyCoder/e991809dbb4194dc5af00d6422083f99) are some
decimal values you can use

Solidity:

```
// SPDX-License-Identifier: GPL-3.0
pragma solidity 0.8.24;

contract Hex {
    constructor(uint256 value){
        x = value;
    }
    
    
    uint256 x;
    function hexmultiply() public view returns (uint256) {
    uint256 tempValue = x;
    unchecked{
    while(tempValue >= 256) {
        tempValue = tempValue >> 4;
    }

    //0xbe.....
    if (tempValue == 190) {
        return x/4;
    }
    else if (tempValue == 222) {
        return x * 4;
    }
    else return x;

}
}
}
```

Yul:

```
// SPDX-License-Identifier: GPL-3.0
pragma solidity 0.8.24;

contract Hex {

    constructor(uint256 value){
        x = value;
    }
    
    uint256 x;

    function hexmultiply() public view returns (uint256) {
        uint256 result;

        assembly {
            // Load the value of `x` from storage. Assuming `x` is stored at slot 0.
            let tempValue := sload(0)

            // Perform the loop equivalent in Yul to shift `tempValue` right by 4 bits until it's less than 256.
            for { } gt(tempValue, 255) { } {
                tempValue := shr(4, tempValue)
            }

            // Check if `tempValue` equals 190 (0xBE) or 222 (0xDE) and multiply/divide `x` accordingly.
            switch tempValue
            case 190 { // If `tempValue` is 0xBE, divide `x` by 4.
                result := div(sload(0), 4)
            }
            case 222 { // If `tempValue` is 0xDE, multiply `x` by 4.
                result := mul(sload(0), 4)
            }
            default { // Otherwise, return `x` as is.
                result := sload(0)
            }
        }

        return result;
    }
}

```

`uint256 deValue = 100651486825157721501865874785935801379319359184012953785965706769510029551182;`

Solidity: 5772 gas

Yul: 5774 gas

`uint256 beValue = 86177475670493197073919501659849812897660611100807883281033508768520888346190;`

Solidity: 5766 gas

Yul: 5763 gas

But their might be a better way to implement this in Yul...
