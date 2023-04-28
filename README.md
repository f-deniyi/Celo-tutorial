# Writing and Running Celo Contract Tests using Hardhat and Solidity for Beginners

Celo is an open-source blockchain platform that enables developers to build decentralized applications (dApps) and smart contracts. Smart contracts are self-executing contracts with the terms of the agreement between buyer and seller being directly written into code. Testing your smart contracts is a crucial step in the development process to ensure that they are functioning correctly and as intended.

In this beginner-friendly tutorial, we will walk you through the process of writing and running tests for Celo smart contracts using the Hardhat development environment and the Solidity programming language.

Prerequisites

Before we begin, you should have the following prerequisites:

- Basic understanding of JavaScript
- Familiarity with Solidity programming language
- Node.js and npm installed on your machine
- A Celo account to deploy and test the contract
- A text editor or IDE for writing code (e.g., Visual Studio Code, Sublime Text, or Atom)

Step 1: Set up your Development Environment

We will be using Hardhat, a development environment that makes it easy to compile, deploy, and test your smart contracts. To install Hardhat, open your terminal or command prompt and navigate to your project directory. Then, run the following command:

```
npm install --save-dev hardhat
```

Once Hardhat is installed, create a new project by running the following command:

```
npx hardhat
```

This will prompt you to choose a network. Select the default option (localhost) and press Enter.

Step 2: Write Your Smart Contract

Next, you will need to write your smart contract. For this tutorial, we will use a simple example contract that increments a counter when a function is called. Open your text editor and create a new file called `Counter.sol`. Add the following code:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Counter {
    uint256 public count;

    function increment() public {
        count++;
    }
}
```

This contract creates a public `count` variable and a public function `increment()` that increments the `count` variable by 1.

Step 3: Write Your Contract Tests

Now that you have written your smart contract, it's time to write your tests. Create a new file called `counter.test.js` in the `test` directory that Hardhat created for you. Add the following code:

```
const { expect } = require("chai");

describe("Counter contract", function () {
  let Counter;
  let counter;

  beforeEach(async function () {
    Counter = await ethers.getContractFactory("Counter");
    counter = await Counter.deploy();
    await counter.deployed();
  });

  it("should start with count of 0", async function () {
    expect(await counter.count()).to.equal(0);
  });

  it("should increment count by 1", async function () {
    await counter.increment();
    expect(await counter.count()).to.equal(1);
  });
});
```

This code imports the `chai` assertion library, describes the `Counter` contract, defines a `beforeEach()` function that deploys the contract before each test, and defines two tests: one that checks that the initial `count` value is 0, and one that increments the `count` value and checks that it has been incremented by 1.

Step 4: Run Your Tests

Now that you have written your tests, you can run them using Hardhat. Open your terminal or command prompt and navigate to your project directory. Then, run the following command to run your tests:

```
npx hardhat test
```

This command will compile your smart contracts, deploy them to a local network, and run your tests .

If your tests pass, you will see output similar to the following:

```
Counter contract

Congratulations, you have successfully written and run tests for your Celo smart contract using Hardhat and Solidity!
```

Step 5: Deploy Your Contract

Before you can deploy your smart contract to the Celo network, you will need to configure your Hardhat environment. Create a new file called `hardhat.config.js` in your project root directory and add the following code:

```

require("@nomiclabs/hardhat-celo");

module.exports = {
networks: {
hardhat: {},
alfajores: {
url: "https://alfajores-forno.celo-testnet.org",
accounts: ["<your-private-key>"]
}
},
solidity: {
version: "0.8.4",
settings: {
optimizer: {
enabled: true,
runs: 200
}
}
}
};
```

This code configures two networks: `hardhat` and `alfajores`. `hardhat` is the default network and is used for local testing. `alfajores` is the Celo testnet network that we will use to deploy our smart contract.

To deploy your smart contract, run the following command:
```
npx hardhat run --network alfajores scripts/deploy.js
```
This command will compile your smart contract, deploy it to the `alfajores` network, and output the contract address. Make note of the contract address, as you will need it to interact with your deployed contract.

Step 6: Interact with Your Deployed Contract

Now that you have deployed your smart contract, you can interact with it using the `celo-blockchain-utils` library. To use this library, install it by running the following command:
npm install --save celo-blockchain-utils


Next, create a new file called `interact.js` and add the following code:
```
const { newKit } = require("@celo/contractkit");
const { privateKeyToAddress } = require("@celo/utils");
const { Counter } = require("../artifacts/contracts/Counter.sol/Counter.json");

const kit = newKit("https://alfajores-forno.celo-testnet.org");
const privateKey = "<your-private-key>";
const account = kit.web3.eth.accounts.privateKeyToAccount(privateKey);
kit.connection.addAccount(account.privateKey);

async function main() {
const address = privateKeyToAddress(privateKey);
const counter = new kit.web3.eth.Contract(Counter.abi, "<your-contract-address>");
const initialCount = await counter.methods.count().call();
console.log(Initial count: ${initialCount});

await counter.methods.increment().send({ from: address });

const newCount = await counter.methods.count().call();
console.log(New count: ${newCount});
}

main();
```

This code imports the `newKit()` function from the `@celo/contractkit` library, as well as the `privateKeyToAddress()` and `Counter` objects from your compiled smart contract. It then creates a new Celo kit and adds your private key to the connection so that you can interact with the network. Finally, it defines a `main()` function that retrieves the initial `count` value from your deployed contract, increments it, and retrieves the new `count` value.

To run this code, run the following command:
```
node interact.js
```

This command will output the initial and new `count` values.

Congratulations, you have successfully written and deployed a Celo smart contract and interacted with it using Hardhat and Solidity!

Conclusion

In this tutorial, we have walked you through the process of writing and testing a Celo smart contract using Hardhat and Solidity. We have also shown you how to deploy your contract to the Celo testnet and interact with it using the celo-blockchain-utils library.

While this tutorial covers the basics of Celo smart contract development, there is still much more to learn. We encourage you to continue exploring the Celo network and its development ecosystem to gain a deeper understanding of how to build decentralized applications on this platform.

Some useful resources for further learning include:

Celo Developer Documentation: https://docs.celo.org/developer-guide
Celo Forum: https://forum.celo.org/
Celo Discord: https://discord.gg/6yWMkgM
Hardhat Documentation: https://hardhat.org/getting-started/
We hope you have found this tutorial helpful and wish you the best of luck in your Celo development journey!

