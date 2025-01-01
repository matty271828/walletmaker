# Airgapped Smart Contract Deployment Workflow

Smart contracts are self-executing contracts with the terms of the agreement directly written into code. 
They are deployed on a blockchain and can be used to automate the execution of agreements or to create complex decentralized applications.

## Task 

We want to create a smart contract that will block a user from sending funds out for a specified amount of time,
but will allow incoming funds. This will enforce the user to hold their funds and not be tempted to sell. 

## Prerequisites

1. Airgapped Computer: 
This is a computer that has no internet connection and is isolated from the rest of your network. It's where you will perform sensitive operations, like signing transactions.

See [bitcoin/bitcoin.md](bitcoin/bitcoin.md) for instructions on how to setup a raspberry pi as an airgapped computer.

2. Cold Wallet (e.g., Ledger or Paper Wallet): 
Used to store your private keys securely. The cold wallet should never be exposed to the internet.

See [ethereum/ethereum.md](ethereum/ethereum.md) for instructions on how to setup a cold wallet for Ethereum.

3. USB Drive: 
Used to transfer data between the airgapped computer and a device connected to the internet (without directly connecting your airgapped computer to the network).

4. Geth or Offline Signing Tools: 

These tools will allow you to sign transactions offline.

See [ethereum/ethereum.md](ethereum/ethereum.md) for instructions on how to setup and use Geth.

5. Remix IDE (or similar): 

A web-based IDE used for creating and compiling the smart contract, but you will use this on a machine connected to the internet and then transfer the compiled bytecode to the airgapped computer.

## Step 1: Write and Compile the Smart Contract on an Online Computer

1. **Write the smart contract in Solidity.**

On a computer connected to the internet, write the smart contract in Solidity using Remix IDE or a similar tool.

Create a new file and name it `LockFunds.sol`. Paste the following code into the file:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract LockEthereum {

    address public restrictedAddress;
    uint256 public restrictionEndTime;

    constructor(address _address) {
        restrictedAddress = _address;
        restrictionEndTime = block.timestamp + 365 days; // Locks for 1 year
    }

    modifier isNotRestricted() {
        require(msg.sender != restrictedAddress || block.timestamp >= restrictionEndTime, "Ethereum address is restricted.");
        _;
    }

    receive() external payable {}

    function withdraw(uint256 amount) external isNotRestricted {
        require(address(this).balance >= amount, "Insufficient balance.");
        payable(msg.sender).transfer(amount);
    }

    function getBalance() public view returns (uint256) {
        return address(this).balance;
    }

    function getRestrictionEndTime() public view returns (uint256) {
        return restrictionEndTime;
    }

    function isRestrictionLifted() public view returns (bool) {
        return block.timestamp >= restrictionEndTime;
    }
}
```

2. **Compile the smart contract.**

Once you’ve written the contract, go to the Solidity Compiler tab in Remix. Choose the appropriate compiler version (use at least 0.8.0).
Click Compile LockEthereum.sol. Once the contract is compiled, you'll see the bytecode and ABI.


3. **Save and Transfer Contract Bytecode**

In Remix, under the Deployed Contracts tab, copy the bytecode (the compiled contract code) to a text file.
Save the contract bytecode and ABI to a your USB drive. 

4. **Use Airgapped Computer for Offline Signing**

- Transfer the contract bytecode and ABI to the airgapped computer. 

- On the airgapped computer, open the Geth client.

- Import your private key into Geth if it’s not already there (using the account import command):

```
geth account import /path/to/private-key-file
```

- Use Geth to prepare and sign the transaction to deploy your smart contract. First, you’ll need to prepare a deployment transaction in the following format:

```
geth --datadir /path/to/data/dir --networkid 1 --port 30303 console
```

- In the Geth console, use the following command to deploy your contract:

```
var contract = web3.eth.contract(abiArray); // Add your contract ABI here
var bytecode = "0x..."; // Add your compiled bytecode here
var tx = contract.new({data: bytecode, from: 'your_wallet_address', gas: 3000000}, function(error, contractInstance) {
  if (!error) {
    console.log("Contract deployed at address: " + contractInstance.address);
  }
});
```

- Sign the transaction with your private key using Geth:

```
eth.sendTransaction({from: 'your_wallet_address', to: '0x0000000000000000000000000000000000000000', data: bytecode, gas: 3000000});
```

Note: The transaction will be signed offline on your airgapped machine, and the signed transaction can then be transferred via USB for broadcasting.

## Step 5: Transfer the Signed Transaction to an Online Machine

Transfer the signed transaction from the airgapped computer to the USB drive.
Move the USB drive to a computer with internet access.

## Step 6: Broadcast the Signed Transaction

On the internet-connected computer, open MyEtherWallet (MEW) or MetaMask (or any Ethereum wallet that supports broadcasting signed transactions).
Select the “Send Offline Transaction” option in MEW or import the signed transaction into MetaMask.
Broadcast the signed transaction to the Ethereum network.
Once the transaction is broadcasted, your smart contract will be deployed!

## Conclusion

By following these steps, you can airgap the entire process of smart contract deployment, keeping your private keys completely offline throughout the process. This way, your private credentials are never exposed to the internet, significantly reducing the risk of compromise while deploying Ethereum smart contracts.

