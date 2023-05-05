# Writing & Running Celo Contract Tests Using Hardhat and Solidity for Beginners

## Table of Contents
- [Writing \& Running Celo Contract Tests Using Hardhat and Solidity for Beginners](#writing--running-celo-contract-tests-using-hardhat-and-solidity-for-beginners)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Pre-requisites](#pre-requisites)
  - [Requirements](#requirements)
  - [Step 1: Set Up Your Development Environment](#step-1-set-up-your-development-environment)
  - [Step 2: Write Your Smart Contract](#step-2-write-your-smart-contract)
  - [Step 3: Write Your Contract Tests](#step-3-write-your-contract-tests)
  - [Step 4: Run Your Tests](#step-4-run-your-tests)
  - [Step 5: Deploy Your Contract](#step-5-deploy-your-contract)
  - [Step 6: Interact With Your Deployed Contract](#step-6-interact-with-your-deployed-contract)
  - [Conclusion](#conclusion)


## Introduction

Celo is an open-source blockchain platform that enables developers to build decentralized applications (dApps) and Smart Contracts. Smart contracts refer to computer protocols that digitally facilitate the verification, control, or execution of an agreement. Testing smart contracts is a crucial step in the development process to ensure that they function correctly and as intended.

In this beginner-friendly tutorial, we will walk you through the process of writing and running tests for Celo Smart Contracts using the Hardhat development environment and the Solidity programming language.

## Pre-requisites

Before we begin, you should have the following pre-requisites:

- Understanding of [JavaScript](https://www.javascript.com/).
- Familiarity with [Solidity](https://soliditylang.org/) programming language

## Requirements

- Install [Node.js](https://nodejs.org/en/download) and [npm](https://www.npmjs.com/package/download)
- A [Celo account](https://celowallet.app/setup) to deploy and test the contract.
- A text editor or IDE for writing code (e.g: [Visual Studio Code](https://code.visualstudio.com/) or [Sublime Text](https://www.sublimetext.com/3) or [Atom](https://sourceforge.net/projects/atom.mirror/))

## Step 1: Set Up Your Development Environment

To get started, we will need to create a project directory and install some libraries and packages. Open a terminal and use the following command to create a new folder:

```bash
mkdir tutorial
```
To change the directory to the `tutorial` folder, use the following command:

```bash
cd tutorial
```

Next, we will need to initialize a `package.json` file using the following command:

```bash
npm init -y
```

We will now install Hardhat. Hardhat is a development environment that makes it easy to compile, deploy and test your Smart Contracts. To install Hardhat and some dependencies we will need, run the following command:

```bash
npm install --save-dev hardhat @nomiclabs/hardhat-waffle ethereum-waffle chai
```

Once Hardhat is installed, create a new Hardhat project by running the following command:

```bash
npx hardhat init
```

This will prompt you to choose an option. Select the default option (create a JavaScript project) and press Enter. Make sure to follow any instructions that Hardhat logs to the console.

## Step 2: Write Your Smart Contract

Next, you will need to write your Smart Contract. For this tutorial, we will use a simple example contract that increments a counter when a function is called. Open your text editor and create a new file called `Counter.sol` in the `contracts` folder of your project. Add the following code:

``` solidity
// SPDX-License-Identifier: MIT
// The above line specifies the license type for this contract

pragma solidity ^0.8.0;

// This is the main contract definition
contract Counter {
    // This is a state variable that holds the current count
    uint256 public count;

    // This function increments the count by one
    function increment() public {
        count++;
    }
}

```

This contract creates a public `count` variable and a public function `increment()` that increments the `count` variable by 1.

>**_Note_**: Don't forget to delete the Lock.sol file, as it would otherwise lead to an error when running some commands.

## Step 3: Write Your Contract Tests

Now that you have written your Smart Contract, it's time to write your tests. Create a new file called `counter.test.js` in the `test` directory that Hardhat created for you. Add the following code:

```javascript
const { expect } = require("chai");

// This test suite tests the Counter contract
describe("Counter contract", function () {
  let Counter;
  let counter;

  // This function is run before each test
  beforeEach(async function () {
    // Get the Counter contract factory
    Counter = await ethers.getContractFactory("Counter");

    // Deploy a new Counter contract instance
    counter = await Counter.deploy();

    // Wait for the contract deployment to complete
    await counter.deployed();
  });

  // This test case checks that the initial value of count is 0
  it("should start with count of 0", async function () {
    expect(await counter.count()).to.equal(0);
  });

  // This test case checks that incrementing count increases its value by 1
  it("should increment count by 1", async function () {
    await counter.increment();
    expect(await counter.count()).to.equal(1);
  });
});
```

This code imports the `chai` assertion library, describes the `Counter` contract, defines a `beforeEach()` function that deploys the contract before each test and defines two tests: one that checks that the initial `count` value is 0, and one that increments the `count` value and checks that it has been incremented by 1.

## Step 4: Run Your Tests

Now that you have written your tests, you can run them using Hardhat. Open your terminal or command prompt and navigate to your project directory. Then, run the following command to run your tests:

```bash
npx hardhat test
```

This command will compile your smart contracts, deploy them to a local network, and run your tests:
If your tests pass, you will see an output similar to the following:

```bash
  Counter contract
    √ should start with count of 0
    √ should increment count by 1


  2 passing (8s)
```

## Step 5: Deploy Your Contract

Before you can deploy your Smart Contract to the Celo network, you will need to configure your Hardhat environment. Create a new file called `hardhat.config.js` in your project root directory and add the following code:

```javascript

require("@nomiclabs/hardhat-waffle");

// Export the Hardhat configuration object
module.exports = {
  // Specify the networks to be used
  networks: {
    // Local development network
    hardhat: {},
    // Celo Alfajores testnet
    alfajores: {
      url: "https://alfajores-forno.celo-testnet.org",
      // Replace <your-private-key> with your own private key
      accounts: ["<your-private-key>"],
      chainId: 44787,
    }
  },
  // Specify the version of Solidity to be used
  solidity: {
    version: "0.8.0",
    // Specify settings for the Solidity compiler
    settings: {
      // Enable the optimizer
      optimizer: {
        enabled: true,
        // Specify the number of optimization runs
        runs: 200
      }
    }
  }
};

```
This code configures two networks: `hardhat` and `alfajores`. `hardhat` is the default network and is used for local testing. `alfajores` is the Celo testnet network that we will use to deploy our Smart Contract.


Next, go to `scripts/deploy.js` and replace the code with the following:

```javascript
// We require the Hardhat Runtime Environment explicitly here. This is optional
// but useful for running the script in a standalone fashion through `node <script>`.
//
// You can also run a script with `npx hardhat run <script>`. If you do that, Hardhat
// will compile your contracts, add the Hardhat Runtime Environment's members to the
// global scope, and execute the script.
const hre = require("hardhat");

async function main() {

  const Counter = await hre.ethers.getContractFactory("Counter");
  const counter = await Counter.deploy();

  await counter.deployed();

  console.log(
    `Counter deployed to address: ${counter.address}`);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});

```

To deploy your smart contract, run the following command:

```bash
npx hardhat run --network alfajores scripts/deploy.js
```

This command will compile your smart contract, deploy it to the `Alfajores` network, and output the contract address. Make note of the contract address, as you will need it to interact with your deployed contract.

## Step 6: Interact With Your Deployed Contract

Now that you have deployed your Smart Contract, you can interact with it using the `@celo/contractkit` library. To use this library, install it by running the following command:

```bash
npm install --save @celo/contractkit
```

Next, create a new file in the `scripts` folder called `interact.js` and add the following code:

```javascript
/// Import necessary packages and dependencies
const { newKit } = require("@celo/contractkit");
const { abi } = require("../artifacts/contracts/Counter.sol/Counter.json");

// Create a new instance of ContractKit connected to the Celo testnet
const kit = newKit("https://alfajores-forno.celo-testnet.org");

// Set the private key for the account that will be interacting with the smart contract
const privateKey =
	"<PRIVATE_KEY>";
const account = kit.web3.eth.accounts.privateKeyToAccount(privateKey);

// Add the account to the connection to enable transactions
kit.connection.addAccount(account.privateKey);
kit.defaultAccount = account.address;

// Define an async function to interact with the smart contract
async function main() {
	// Create a new instance of the Counter contract using its ABI and address
	const counter = new kit.web3.eth.Contract(
		abi,
		"<Contract_Address>"
	);

	// Call the 'count' function to get the initial count value
	const initialCount = await counter.methods.count().call();

	// Log the initial count value to the console
	console.log(`Initial count: ${initialCount}`);

	// Send a transaction to increment the count by 1
  const txo = await counter.methods.increment();
  const tx = await kit.sendTransactionObject(txo, { from: kit.defaultAccount });
  const receipt = await tx.waitReceipt();

	// Call the 'count' function again to get the new count value
	const newCount = await counter.methods.count().call();

	// Log the new count value to the console
	console.log(`New count: ${newCount}`);
}

// Call the main function to execute the program
main();

```

This code imports the `newKit()` function from the `@celo/contractkit` library, as well as the `privateKeyToAddress()` and `Counter` objects from your compiled Smart Contract. It then creates a new Celo kit and adds your private key to the connection so that you can interact with the network. Finally, it defines a `main()` function that retrieves the initial `count` value from your deployed contract, increments it and retrieves the new `count` value.

To run this code, run the following command:

```bash
npx hardhat run scripts/interact.js
```

This command will output the initial and new `count` values.

Congratulations!! You have successfully written and deployed a Celo Smart Contract and interacted with it using Hardhat and Solidity!

## Conclusion

In this tutorial, we have walked you through the process of writing and testing a Celo smart contract using Hardhat and Solidity. We have also shown you how to deploy your contract to the Celo testnet and interact with it using the celo-blockchain-utils library.

While this tutorial covers the basics of Celo smart contract development, there is still much more to learn. We encourage you to continue exploring the Celo network and its development ecosystem to gain a deeper understanding of how to build decentralized applications on this platform.

*Some useful resources for further learning include:*

Celo Developer Documentation: https://docs.celo.org/developer-guide
Celo Forum: https://forum.celo.org/
Celo Discord: https://discord.gg/6yWMkgM
Hardhat Documentation: https://hardhat.org/getting-started/

We hope you have found this tutorial helpful and wish you the best of luck in your Celo development journey!
