### Install requirements
Update
```bash
sudo apt update && sudo apt upgrade -y
```
We need to install npm nodejs git:
```
sudo apt install npm git -y
```
Install nodejs. See recommended version at https://nodejs.org/en/
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```
### Install truffle
```
npm install -g truffle
truffle version
```
You should see output like that:
```
Truffle v5.7.9 (core: 5.7.9)
Ganache v7.7.5
Solidity v0.5.16 (solc-js)
Node v18.14.2
Web3.js v1.8.2
```
## Creating smart contract
Off tutorial: https://docs.zkevm.consensys.net/developers/quickstart/deploy-smart-contract/truffle

Create folder. Change into the new directory:
```
mkdir /root/zkevm-truffle
cd /root/zkevm-truffle
```
Init new project (named `zkevm-truffle` as our folder)
```
truffle init
```
See what we have
```
ls -1
```
### Write the smart contract
Create your smart contract by calling:
```
truffle create contract Token
```
Then, add the following code in the file `Token` by command below:
```
cat > /root/zkevm-truffle/contracts/Token.sol << 'EOF'
pragma solidity 0.8.17;

// SPDX-License-Identifier: MIT

contract Token {
  string public name = "My Token";
  string public symbol = "MTK";
  uint8 public decimals = 18;
  uint256 public totalSupply = 100000000;

  mapping (address => uint256) public balances;
  address public owner;

  constructor() {
    owner = msg.sender;
    balances[owner] = totalSupply;
  }

  function transfer(address recipient, uint256 amount) public {
    require(balances[msg.sender] >= amount, "Insufficient balance.");
    balances[msg.sender] -= amount;
    balances[recipient] += amount;
  }
}
EOF
```
> ❗️DO NOT USE THIS CONTRACT CODE IN PRODUCTION❗️
### Check if it compiles by running
```
truffle compile
```
### Write the migration script
To tell Truffle how, and in what order we want to deploy our smart contracts, we need to write a migration script.

Create `1_deploy_token.js` in the `migrations` directory:
```
cat > /root/zkevm-truffle/migrations/1_deploy_token.js << 'EOF'
const Token = artifacts.require("Token");

module.exports = function (deployer) {
  deployer.deploy(Token);
};
EOF
```
