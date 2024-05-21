# BuidlBase-Berlin

BuidlBase Berlin Workshop - SpeedRunEthereum Token Vendor

1. Go to speedrunethereum.com, we'll be doing the Token Vendor challange. 

https://speedrunethereum.com/challenge/token-vendor

2. Setup 

Download the challenge to your computer and install dependencies by running:

```
git clone https://github.com/scaffold-eth/se-2-challenges.git challenge-2-token-vendor
cd challenge-2-token-vendor
git checkout challenge-2-token-vendor
yarn install
```

In the same terminal, start your local network (a blockchain emulator in your computer):

```
yarn chain 
```

In a new terminal window, 🛰 deploy your contract (locally):

```
cd challenge-2-token-vendor
yarn deploy
```

In a third terminal window, start your 📱 frontend:

```
cd challenge-2-token-vendor
yarn start
```

Open http://localhost:3000 to see the app.

3. Edit YourToken.sol -- Change the address to mint the token to your local address

```
contract YourToken is ERC20 {
	constructor() ERC20("Gold", "GLD") {
		_mint(msg.sender, 1000 * 10 ** 18);
	}
}
```

```
yarn deploy --reset 
```

4. Edit the Vendor.sol contract
```
pragma solidity 0.8.4; //Do not change the solidity version as it negativly impacts submission grading
// SPDX-License-Identifier: MIT

import "@openzeppelin/contracts/access/Ownable.sol";
import "./YourToken.sol";

contract Vendor is Ownable {
	event BuyTokens(address buyer, uint256 amountOfETH, uint256 amountOfTokens);
	event SellTokens(address seller, uint256 amountOfTokens);

	YourToken public yourToken;
	uint256 public constant tokensPerEth = 1;

	constructor(address tokenAddress) {
		yourToken = YourToken(tokenAddress);
	}

	// ToDo: create a payable buyTokens() function:
	function buyTokens() public payable {
		uint256 tokenAmount = msg.value * tokensPerEth;
		yourToken.transfer(msg.sender, tokenAmount);
		emit BuyTokens(msg.sender, msg.value, tokenAmount);
	}

	// ToDo: create a withdraw() function that lets the owner withdraw ETH
	function withdraw() public onlyOwner {
		(bool success, ) = payable(address(owner())).call{
			value: address(this).balance
		}("");
	}

	// ToDo: create a sellTokens(uint256 _amount) function:
	function sellTokens(uint256 _amount) public {
		// Ensure the amount is greater than zero
		require(_amount > 0, "Amount must be greater than zero");

		// Call the allowance function on the token contract
		uint256 allowedAmount = yourToken.allowance(msg.sender, address(this));
		require(
			allowedAmount >= _amount,
			"You need to approve the tokens first"
		);

		// Transfer the tokens from the sender to this contract
		yourToken.transferFrom(msg.sender, address(this), _amount);

		// Emit the SellTokens event
		emit SellTokens(msg.sender, _amount);
	}
}
```




