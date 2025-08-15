# Builder Package

## Overview

The builder package is responsible for constructing new blocks in the Juno blockchain. It manages the lifecycle of block building, including initializing preconfirmed blocks, executing transactions, and finalizing blocks for inclusion in the chain. The package integrates closely with the blockchain state, virtual machine execution, and transaction mempool to ensure blocks are built correctly and efficiently.

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

The builder package is designed around a modular architecture that separates concerns between block construction, transaction execution, and state management. It interacts with the blockchain to retrieve the current chain head and state, uses an executor component to run transactions within a virtual machine environment, and maintains a preconfirmed block state that accumulates transactions and state changes before finalization.

The package supports a layered approach where the builder initializes a block template, executes batches of transactions against the current state, and then finalizes the block by applying state updates and generating commitments. This design allows for efficient block building and easy integration with consensus and networking layers.

### Component Overview

- **Builder**: The main component responsible for managing the block building process, including initialization, transaction execution, and finalization.
- **Executor**: Handles the execution of transactions within the virtual machine and applies state changes to the preconfirmed block.
- **BuildState**: Represents the current state of the block being built, including the preconfirmed block, gas consumption, and revealed block hash.
- **BuildResult**: Contains the results of the block building process, including the finalized preconfirmed block and simulation results.

## Core Components

### Builder (builder.go)

The Builder struct orchestrates the block building process. It initializes preconfirmed blocks based on the current blockchain head, manages transaction execution through the executor, and finalizes blocks by applying state updates and generating signatures.

Key files:
- builder.go - Main builder implementation managing block construction lifecycle

### Executor (executor.go)

The Executor interface and its implementation handle the execution of transactions against the blockchain state using the virtual machine. It processes transaction batches, manages declared contract classes, and updates the preconfirmed block with execution results.

Key files:
- executor.go - Transaction execution and state application logic

### BuildState (state.go)

BuildState maintains the mutable state of the block under construction. It tracks the preconfirmed block, gas consumed during transaction execution, and the revealed block hash used for certain protocol operations. It also provides cloning and clearing functionality to manage state transitions.

Key files:
- state.go - Build state management and cloning utilities

### BuildResult (result.go)

BuildResult encapsulates the outcome of the block building process. It includes the finalized preconfirmed block, simulation results from executing the block, and gas consumption metrics. It also provides methods to generate proposal commitments used in consensus.

Key files:
- result.go - Build result representation and proposal commitment generation

## Key Features

1. **Preconfirmed Block Initialization**
   - Initializes a new block template based on the current blockchain head, setting up headers and default values for transaction and event counts.

2. **Transaction Execution and State Updates**
   - Executes batches of transactions using a virtual machine, applies state diffs, manages declared contract classes, and updates the preconfirmed block accordingly.

3. **Gas Consumption Tracking**
   - Accumulates L2 gas consumed during transaction execution to provide accurate gas usage metrics for the block.

4. **Block Finalization**
   - Finalizes the block by applying state updates, generating commitments, and preparing the block for inclusion in the blockchain.

5. **Versioning and Protocol Compliance**
   - Supports protocol versioning and manages gas price conversions between L1 and L2 layers to ensure compliance with network parameters.

## Usage

### Basic Integration

To use the builder package, initialize a Builder instance by providing the blockchain and an Executor implementation. Use the Builder to create a preconfirmed block, run transactions, and finalize the block.

### Configuration

The Builder requires configuration parameters such as the builder address, gas prices for L1 and L2, timestamp, and L1 data availability mode. These parameters are encapsulated in the BuildParams struct and used during block initialization.

## API Reference

### Main Types

#### Builder

Manages the block building process including initialization, transaction execution, and finalization.

#### Executor

Interface defining methods to run transactions and finish block execution.

#### BuildState

Represents the current state of the block being built, including the preconfirmed block and gas consumption.

#### BuildResult

Contains the results of the block building process, including the finalized block and simulation data.

### Key Functions

#### New (Builder)

Creates a new Builder