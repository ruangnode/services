---
description: Use sepolia chains
---

# Create Solidity Contract

````
// Some code```remix-solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Register {
    string public github;
    address public owner;
    
    struct Referral {
        address referralAddress;
        string referralString;
    }
    
    Referral[] public referrals;
    
    constructor() {
        github = "ruangnode"; //change with your github account
        owner = 0x188563D2EA4d89715f7df2E1F613XXXXXXXX; //change with your address
    }
    
    function addReferral(address _referralAddress, string memory _referralString) external {
        require(msg.sender == owner, "Only the owner can add referrals.");
        referrals.push(Referral(_referralAddress, _referralString));
    }
    
    function totalReferrals() public view returns (uint256) {
        return referrals.length;
    }
}
```
````
