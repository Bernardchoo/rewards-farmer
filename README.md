# rewards-farmer
open-source factory for managing rewards tokens

## Contracts

### FarmerFactory.sol

* uses OpenZeppelin's [ProxyFactory](https://github.com/OpenZeppelin/openzeppelin-sdk/blob/master/packages/lib/contracts/upgradeability/ProxyFactory.sol) contract
* `deployProxy` deploys a clone of logic contract
* `transferToken` transfers amount of token from sender's proxy contract to recipient's proxy contract. If recipient does not have a proxy contract, one is deployed on their behalf. 
* `mapping (address => address) public farmerProxy;` maps user address to address of deployed clone

### Farmer.sol

* sets user address as owner
<!-- * `mintcDAI` mints cDAI
* `claimCOMP` calls Compounds claimComp function -->

## How to use

Inherit from the FarmerFactory contract in your token contract. Inherit from the Farmer contract to create your own Farmer contract that mints your rewards bearing token. The FarmerFactory deploys proxy contracts that delegate to your Farmer contract.

### Example

```
pragma solidity ^0.5.3;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "./Factory.sol";
import "./CDAIWrapper.sol";

contract saveDAI is Factory, ERC20 {

    constructor() public ERC20("TokenS", "TKS") {}

    function mint(uint256 amount) public {
        // deploy clone
        address clone = deployProxy();

        // transfer DAI to proxy contract
        require(
            dai.transferFrom(
                msg.sender,
                clone,
                amount
            )
        );

        // mintcDAI in cDAIwrapper contract
        CDAIWrapper(clone).mintcDAI();

        // mints TokenS
    }

    function withdrawCOMP() public
        address wrapper = cDAIOwner[msg.sender];
        CDAIWrapper(wrapper).withdrawCOMP(msg.sender); 
    }

    function getTotalCOMPEarned() public view
        address wrapper = cDAIOwner[msg.sender];
        CDAIWrapper(wrapper).getTotalCOMPEarned(msg.sender); 
    }

    function transfer(address to, uint256 amount) public override {
        // transfer amount of TokenS
        transferCDAI(to, amount);
    }

}
```


`ganache-cli -f https://mainnet.infura.io/v3/d68ae769e0734ac7a6475ed2fce271cf --unlock "0xc0a47dFe034B400B47bDaD5FecDa2621de6c4d95" --unlock "0x6B175474E89094C44Da98b954EedeAC495271d0F" --unlock "0x98CC3BD6Af1880fcfDa17ac477B2F612980e5e33" --unlock "0x5d3a536E4D6DbD6114cc1Ead35777bAB948E3643" --unlock "0x274d9e726844ab52e351e8f1272e7fc3f58b7e5f" --unlock "0x076c95c6cd2eb823acc6347fdf5b3dd9b83511e4" --unlock "0xcae687969d3a6c4649d114b1c768d5b1deae547b" --unlock "0xd89b6d5228672ec03ab5929d625e373b4f1f25f3"`

`truffle test --network mainlocal`