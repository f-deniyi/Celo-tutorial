# Writing & Running Celo Contract Tests using Hardhat and Solidity for Beginners:

Celo is an open-source blockchain platform that enables developers to build decentralized applications (dApps) and Smart Contracts. Smart Contracts are self-executing contracts with the terms ofagree ment between buyer and seller being directly written into code. Testing your Smart Contracts is a crucial step in the development process to ensure that they are functioning correctly and as intended.

In this beginner-friendly tutorial, we will walk you through the process of writing and running tests for Celo Smart Contracts using the Hardhat development environment and the Solidity programming language.

# Pre-requisites:

Before we begin, you should have the following pre-requisites:

- Understanding of JavaScript.
- Familiarity with Solidity programming language
- Install [Node.js](https://nodejs.org/en/download) and [npm](https://www.npmjs.com/package/download)
- A [Celo account](https://celowallet.app/setup) to deploy and test the contract.
- A text editor or IDE for writing code (e.g: [Visual Studio Code](https://code.visualstudio.com/) or [Sublime Text](https://www.sublimetext.com/3) or [Atom](https://sourceforge.net/projects/atom.mirror/)

## Step 1: Set up your Development Environment:

We will be using Hardhat, which is a development environment that makes it easy to compile, deploy and test your Smart Contracts. To install Hardhat, open your terminal or command prompt and navigate to your project directory. Then, run the following command:

```bash
npm install --save-dev hardhat
```

Once Hardhat is installed, create a new project by running the following command:

```bash
npx hardhat
```

This will prompt you to choose a network. Select the default option (localhost) and press Enter.

## Step 2: Write Your Smart Contract:

Next, you will need to write your Smart Contract. For this tutorial, we will use a simple example contract that increments a counter when a function is called. Open your text editor and create a new file called `Counter.sol`. Add the following code:

``` solidity
/**
 * @title Counter
 * @dev A simple smart contract that allows you to increment and decrement a counter
 * using the SafeMath library to prevent overflow and underflow errors.
 */
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/utils/math/SafeMath.sol";

contract Counter {
    using SafeMath for uint256;

    address public owner;
    uint256 public count;

    event CountIncremented(uint256 newCount);
    event CountDecremented(uint256 newCount);

    /**
     * @dev Modifier to ensure that only the contract owner can call a function.
     */
    modifier onlyOwner() {
        require(msg.sender == owner, "Only the contract owner can call this function.");
        _;
    }

    /**
     * @dev Constructor function that initializes the owner and count variables.
     */
    constructor() {
        owner = msg.sender;
        count = 0;
    }

    /**
     * @dev Function to increment the counter by one.
     */
    function increment() public {
        count = count.add(1);
        emit CountIncremented(count);
    }

    /**
     * @dev Function to decrement the counter by one.
     */
    function decrement() public {
        count = count.sub(1);
        emit CountDecremented(count);
    }
}


```

This contract creates a public `count` variable and a public function `increment()` that increments the `count` variable by 1.

## Step 3: Write Your Contract Tests:

Now that you have written your Smart Contract, it's time to write your tests. Create a new file called `counter.test.js` in the `test` directory that Hardhat created for you. Add the following code:

`const { expect } = require("chai");

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

This code imports the `chai` assertion library, describes the `Counter` contract, defines a `beforeEach()` function that deploys the contract before each test, and defines two tests: one that checks that the initial `count` value is 0, and one that increments the `count` value and checks that it has been incremented by 1.

## Step 4: Run Your Tests:

Now that you have written your tests, you can run them using Hardhat. Open your terminal or command prompt and navigate to your project directory. Then, run the following command to run your tests:

```bash
npx hardhat test
```

This command will compile your smart contracts, deploy them to a local network, and run your tests:
If your tests pass, you will see output similar to the following:

```bash
Counter contract

Congratulations!! You have successfully written and run tests for your Celo Smart Contract using Hardhat and Solidity!
```

## Step 5: Deploy Your Contract:

Before you can deploy your Smart Contract to the Celo network, you will need to configure your Hardhat environment. Create a new file called `hardhat.config.js` in your project root directory and add the following code:

```javascript

// Import the Hardhat Celo plugin
require("@nomiclabs/hardhat-celo");

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
      accounts: ["<your-private-key>"]
    }
  },
  // Specify the version of Solidity to be used
  solidity: {
    version: "0.8.4",
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

To deploy your Smart Contract, run the following command:

```bash
npx hardhat run --network alfajores scripts/deploy.js
```

This command will compile your smart contract, deploy it to the `alfajores` network, and output the contract address. Make note of the contract address, as you will need it to interact with your deployed contract.

## Step 6: Interact with Your Deployed Contract:

Now that you have deployed your Smart Contract, you can interact with it using the `celo-blockchain-utils` library. To use this library, install it by running the following command:

```bash
npm install --save celo-blockchain-utils
```

Next, create a new file called `interact.js` and add the following code:

```javascript
// Import necessary packages and dependencies
const { newKit } = require("@celo/contractkit");
const { privateKeyToAddress } = require("@celo/utils");
const { Counter } = require("../artifacts/contracts/Counter.sol/Counter.json");

// Create a new instance of ContractKit connected to the Celo testnet
const kit = newKit("https://alfajores-forno.celo-testnet.org");

// Set the private key for the account that will be interacting with the smart contract
const privateKey = "<your-private-key>";
const account = kit.web3.eth.accounts.privateKeyToAccount(privateKey);

// Add the account to the connection to enable transactions
kit.connection.addAccount(account.privateKey);

// Define an async function to interact with the smart contract
async function main() {
// Get the address associated with the private key
const address = privateKeyToAddress(privateKey);

// Create a new instance of the Counter contract using its ABI and address
const counter = new kit.web3.eth.Contract(Counter.abi, "<your-contract-address>");

// Call the 'count' function to get the initial count value
const initialCount = await counter.methods.count().call();

// Log the initial count value to the console
console.log(Initial count: ${initialCount});

// Send a transaction to increment the count by 1
await counter.methods.increment().send({ from: address });

// Call the 'count' function again to get the new count value
const newCount = await counter.methods.count().call();

// Log the new count value to the console
console.log(New count: ${newCount});
}

// Call the main function to execute the program
main();

```

This code imports the `newKit()` function from the `@celo/contractkit` library, as well as the `privateKeyToAddress()` and `Counter` objects from your compiled Smart Contract. It then creates a new Celo kit and adds your private key to the connection so that you can interact with the network. Finally, it defines a `main()` function that retrieves the initial `count` value from your deployed contract, increments it and retrieves the new `count` value.

To run this code, run the following command:

```bash
node interact.js
```

This command will output the initial and new `count` values.

Congratulations!! You have successfully written and deployed a Celo Smart Contract and interacted with it using Hardhat and Solidity!

# Conclusion:

In this tutorial, we have walked you through the process of writing and testing a Celo smart contract using Hardhat and Solidity. We have also shown you how to deploy your contract to the Celo testnet and interact with it using the celo-blockchain-utils library.

While this tutorial covers the basics of Celo smart contract development, there is still much more to learn. We encourage you to continue exploring the Celo network and its development ecosystem to gain a deeper understanding of how to build decentralized applications on this platform.

*Some useful resources for further learning include:*

Celo Developer Documentation: https://docs.celo.org/developer-guide
Celo Forum: https://forum.celo.org/
Celo Discord: https://discord.gg/6yWMkgM
Hardhat Documentation: https://hardhat.org/getting-started/

We hope you have found this tutorial helpful and wish you the best of luck in your Celo development journey!
