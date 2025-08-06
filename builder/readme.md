# Builder Package

## Overview

The builder package in the Juno blockchain is responsible for constructing new blocks by executing transactions and managing state updates. It provides the logic to initialize preconfirmed blocks, run transactions against the current blockchain state, and finalize blocks for inclusion in the chain. The package integrates with the blockchain, virtual machine, and mempool components to ensure accurate and efficient block building.

## Table of Contents

- [Architecture](#architecture)
- [Core Components](#core-components)
- [Key Features](#key-features)
- [Usage](#usage)
- [API Reference](#api-reference)
- [Examples](#examples)
- [Contributing](#contributing)
- [License](#license)

## Architecture

The builder package is designed to facilitate the creation of new blocks in the blockchain by coordinating transaction execution and state management. It interacts closely with the blockchain state, the virtual machine for transaction execution, and the mempool for transaction retrieval. The package maintains a preconfirmed block state that is updated as transactions are executed and finalized once all processing is complete.

The architecture separates concerns into distinct components such as the builder logic, transaction executor, and state management. This modular design allows for clear responsibilities and easier maintenance.

### Component Overview

- **Builder**: Manages the overall block building process including initialization, transaction execution, and finalization.
- **Executor**: Handles the execution of transactions using the virtual machine and applies state changes.
- **BuildState**: Represents the current state of the block being built, including the preconfirmed block and gas consumption.
- **BuildResult**: Contains the results of the build process, including the finalized block and simulation results.

## Core Components

### Builder (builder.go)

The Builder struct is the main entry point for block construction. It initializes preconfirmed blocks, manages transaction execution, and finalizes blocks for the blockchain. It depends on the blockchain and an executor interface to perform its operations.

Key files:
- builder.go - Main builder implementation managing block construction

### Executor (executor.go)

The Executor interface defines methods to run transactions and finish the build process. The executor struct implements this interface by executing transactions on the virtual machine, updating state, and handling declared contract classes.

Key files:
- executor.go - Transaction execution and state update logic

### BuildState (state.go)

BuildState holds the current preconfirmed block, gas consumption, and revealed block hash during the build process. It provides methods to access and clone the pending block state.

Key files:
- state.go - Build state representation and cloning utilities

### BuildResult (result.go)

BuildResult encapsulates the outcome of the build process, including the preconfirmed block, simulation results, and gas consumed. It also provides a method to generate a proposal commitment for consensus.

Key files:
- result.go - Build result structure and proposal commitment generation

## Key Features

1. **Preconfirmed Block Initialization**
   - Initializes a new block based on the current blockchain head, setting up necessary fields for transaction execution.

2. **Transaction Execution and State Management**
   - Executes batches of transactions using the virtual machine, applies state diffs, and manages declared contract classes.

3. **Gas Consumption Tracking**
   - Tracks L2 gas consumed during transaction execution to support fee calculations and block resource management.

4. **Block Finalization**
   - Finalizes the block by simulating the state update and preparing the block for inclusion in the blockchain.

5. **Proposal Commitment Generation**
   - Generates a proposal commitment containing block metadata and commitments required for consensus.

## Usage

### Basic Integration

To use the builder package, create a Builder instance by providing the blockchain and an executor implementation. Initialize a preconfirmed block with build parameters, run transactions, and finalize the block.

### Configuration

Build parameters include the builder address, timestamps, gas prices, and L1 data availability mode. These parameters are used to configure the preconfirmed block header and gas pricing.

## API Reference

### Main Types

#### Builder

Manages block building operations including initialization, transaction execution, and finalization.

#### Executor

Interface defining methods to run transactions and finish the build process.

#### BuildState

Represents the current state of the block being built, including the preconfirmed block and gas consumption.

#### BuildResult

Contains the results of the build process, including the finalized block and simulation results.

### Key Functions

#### New (Builder)

Creates a new Builder instance with the provided blockchain and executor.

#### InitPreconfirmedBlock

Initializes a new preconfirmed block based on the current blockchain head and build parameters.

#### RunTxns

Executes a batch of transactions against the current build state, updating the preconfirmed block and state diffs.

#### Finish

Finalizes the build process by simulating the block and state update, returning the build result.

#### ProposalCommitment (BuildResult)

Generates a proposal commitment containing block metadata and commitments for consensus.

## Examples

### Starting a Block Build

Initialize a Builder with blockchain and executor dependencies. Use `InitPreconfirmedBlock` to create a new block state.

### Executing Transactions

Use the executor to run transactions on the build state, updating the preconfirmed block and state diffs.

### Finalizing a Block

Call `Finish` on the builder to simulate and finalize the block, producing a build result ready for consensus.

## Contributing

To contribute to the builder package:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests to cover new functionality
5. Submit a pull request for review

## License

This package is licensed under the terms specified in the Juno blockchain repository. Please refer to the repository LICENSE file for detailed license information.