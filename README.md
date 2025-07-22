# Nexus Messaging Boilerplate (Hardhat)

This project is a boilerplate for building and deploying contracts that use Nexus powered cross-chain messaging. It provides ready-to-use scripts for compiling, deploying, and verifying contracts on multiple EVM-compatible chains (currently Sepolia and Arbitrum Sepolia testnets).

## Installation

First, install all dependencies:
```shell
npm install
```

## Setup Process

After installing dependencies, you must provide your deployer private key and Etherscan API key using **both** a `.env` file and Hardhat vars. This ensures compatibility with all scripts and workflows.

### 1. Create a `.env` file in the project root:
```env
PRIVATE_KEY=your_deployer_private_key
ETHERSCAN_API_KEY=your_etherscan_v2_api_key
```

### 2. Set the same secrets using Hardhat vars:
```shell
npx hardhat vars set PRIVATE_KEY
npx hardhat vars set ETHERSCAN_API_KEY
```

You can check your current vars with:
```shell
npx hardhat vars list
```

---

## Quick Start

1. **Compile contracts:**
   ```shell
   npm run compile
   ```
3. **Deploy contracts:**
   ```shell
   npm run deploy
   ```
   - By default, this deploys `MockClient` contracts to Sepolia and Arbitrum Sepolia.
   - The deployment script:
     - Sets the correct Nexus Interchain Security Module (ISM) and mailbox for each contract.
     - Automatically enrolls the peer address of the contract deployed on the other chain (enabling cross-chain messaging out-of-the-box).
   - Deployment details are saved to `deployments/mockclient.json` for later verification or troubleshooting.

4. **Verify contracts:**
   - To verify on Sepolia:
     ```shell
     npm run verify:sepolia
     ```
   - To verify on Arbitrum Sepolia:
     ```shell
     npm run verify:arbitrumsepolia
     ```
   - **Note:** Verification on Arbitrum Sepolia is currently failing due to block explorer/API issues. This will be fixed soon.

5. **Send a cross-chain message:**
   - After deployment, you can send a "Hello World" message from one chain to another using:
     ```shell
     npm run send-message
     ```
   - This script performs the following steps:
     1. Connects to the source chain's Mailbox contract
     2. Calls `quoteDispatch` to get the exact amount of native token (ETH) required for the cross-chain message
     3. Displays the quoted amount needed for the transaction
     4. Sends the message with the exact quoted value attached to the transaction
   - **Important:** The quoted amount is the exact value required by the Hyperlane protocol to process the cross-chain message. This covers:
     - Gas costs on the destination chain
     - Protocol fees
     - Any additional costs for message delivery
   - The message will be processed by the destination chain's Mailbox and delivered to the recipient contract

   Example output:
   ```
   Calling sendHelloWorld on sepolia:0x1234... with destination address 0x5678... and domain 421614
   Message: Hello World!
   Quote for dispatching message: 0.000031460400000001 ETH
   ```


## Troubleshooting & Manual Process

- **Nexus ISM addresses:**
  - Sepolia: `0x2004694c5801e7a6F7C72aDc8275Fd63C3068BCE`
  - Arbitrum Sepolia: `0xAd96506f940e114FF35A9Eb6489e731d66180B99`
- The deployment script uses these by default, but you can override them in `deploy-mockclients.ts` if needed.
- If you need to re-verify or debug, check `deployments/mockclient.json` for contract addresses and constructor arguments.

## Environment Variables

- Create a `.env` file with:
  ```env
  PRIVATE_KEY=your_deployer_private_key
  ETHERSCAN_API_KEY=your_etherscan_v2_api_key
  ```

## Known Issues

- **Verification on Arbitrum Sepolia**: Currently fails due to the block explorer endpoint/API key handling. This will be fixed as soon as upstream support is stable.
- **Node.js Version**: Hardhat only supports Node.js 18.x and 20.x. Using other versions may cause unexpected errors.

## Additional Notes
- The deployment and verification scripts are designed to be run per-network for reliability.
- For custom deployments or additional networks, extend the `CHAIN_CONFIG` in `deploy-mockclients.ts` and update scripts as needed.
- For more information on Nexus and Hyperlane, see the [Nexus documentation](https://docs.hyperlane.xyz/).
