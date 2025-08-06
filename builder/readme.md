# Builder Package

## Overview

The builder package provides the core functionality to construct and finalize blocks within the Juno blockchain. It manages the lifecycle of block building, including initializing preconfirmed blocks, executing transactions, updating state, and producing finalized blocks ready for consensus. The package integrates tightly with the blockchain state, virtual machine execution, and transaction mempool to ensure accurate and efficient block construction.

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

The builder package is designed to facilitate the creation of new blocks by executing transactions against the current blockchain state and managing the resulting state updates. It operates by initializing a preconfirmed block based on the current chain head, running batches of transactions through a virtual machine executor, and finalizing the block with updated state roots and commitments.

The package interacts with several key subsystems: the blockchain for state and headers, the virtual machine for transaction execution, and the mempool for transaction retrieval. It maintains a build state that tracks the preconfirmed block, gas consumption, and revealed block hashes to ensure consistency and correctness during block construction.

### Component Overview

- **Builder**: The main orchestrator responsible for initializing blocks, running transactions, and finalizing blocks.
- **Executor**: Handles the execution of transactions using the virtual machine and applies state changes.
- **BuildState**: Represents the current state of the block being built, including the preconfirmed block and gas usage.
- **BuildResult**: Contains the finalized block information and simulation results after execution.
- **State Management**: Utilities for cloning and managing state diffs and preconfirmed block data.

## Core Components

### Builder (builder.go)

The Builder struct is the central component that manages block construction. It initializes preconfirmed blocks based on the current blockchain head, manages transaction execution through the executor, and finalizes blocks by updating the blockchain state.

Key files:
- builder.go - Main builder implementation managing block lifecycle

### Executor (executor.go)

The Executor interface and its implementation handle the execution of transactions within a block. It runs transactions through the virtual machine, processes declared contract classes, updates state diffs, and prepares receipts. The executor also finalizes execution by simulating the block and producing results.

Key files:
- executor.go - Transaction execution and state update logic

### BuildState (state.go)

BuildState holds the current state of the block under construction, including the preconfirmed block, gas consumed, and the revealed block hash. It provides methods to clone the state and clear pending data, facilitating safe state management during block building.

Key files:
- state.go - Build state representation and cloning utilities

### BuildResult (result.go)

BuildResult encapsulates the outcome of the block building process, including the finalized preconfirmed block, simulation results, and gas consumption. It also provides methods to generate proposal commitments used in consensus.

Key files:
- result.go - Build result structure and proposal commitment generation

## Key Features

1. **Preconfirmed Block Initialization**
   - Initializes a new block based on the current chain head, setting up headers and initial state for transaction execution.

2. **Transaction Execution and State Updates**
   - Executes batches of transactions using a virtual machine, applies state diffs, and manages declared contract classes.

3. **Gas Consumption Tracking**
   - Tracks Layer 2 gas consumption during transaction execution to accurately reflect resource usage.

4. **Block Finalization**
   - Finalizes blocks by simulating execution results, updating commitments, and preparing blocks for consensus.

5. **State Cloning and Management**
   - Provides utilities to clone and manage state diffs and preconfirmed blocks to ensure safe concurrent operations.

## Usage

### Basic Integration

To use the builder package, initialize a Builder instance with the blockchain and an Executor. Use the Builder to create a preconfirmed block, run transactions, and finalize the block.

### Configuration

The Builder requires dependencies such as the blockchain instance and an Executor implementation. The Executor itself requires a virtual machine instance and configuration flags for fee handling and validation.

## API Reference

### Main Types

#### Builder

Manages the block building process including initialization, transaction execution, and finalization.

#### Executor

Interface defining methods to run transactions and finish block execution.

#### BuildState

Represents the current state of the block being built, including the preconfirmed block and gas usage.

#### BuildResult

Contains the finalized block and execution results after building.

### Key Functions

#### New (Builder)

Creates a new Builder instance with the provided blockchain and executor.

#### Finalise

Finalizes a preconfirmed block by updating the blockchain state and signing the block.

#### InitPreconfirmedBlock

Initializes a new preconfirmed block based on the current blockchain head and build parameters.

#### RunTxns

Executes a batch of transactions against the current build state using the executor.

#### Finish

Completes the block building process by simulating execution results and preparing the final build result.

#### NewExecutor

Creates a new Executor instance with the given blockchain, virtual machine, logger, and configuration flags.

#### RunTxns (Executor)

Executes transactions, applies state changes, and updates the preconfirmed block accordingly.

#### Finish (Executor)

Simulates the block execution and returns the simulation results.

#### Clone (BuildState)

Creates a deep copy of the current build state for safe manipulation.

#### ProposalCommitment (BuildResult)

Generates a proposal commitment structure used in consensus from the build result.

## Examples

### Starting a Block Build

Initialize a Builder with blockchain and executor, then create a preconfirmed block with build parameters.

### Executing Transactions

Use the Builder to run batches of transactions on the preconfirmed block, updating state and gas consumption.

### Finalizing a Block

Call the Builder's Finish method to simulate and finalize the block, producing a build result ready for consensus.

## Contributing

To contribute to the builder package:

1. Fork the repository
2. Create a feature branch
3. Implement your changes
4. Add appropriate tests
5. Submit a pull request for review

## License

This package is part of the Juno blockchain project. Please refer to the repository for specific license information.