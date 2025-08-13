# Builder Package

## Overview

The builder package is responsible for constructing new blocks in the Juno blockchain. It manages the lifecycle of block building, including initializing preconfirmed blocks, executing transactions, updating state, and finalizing blocks for inclusion in the chain. The package integrates closely with the blockchain state, transaction mempool, and virtual machine to simulate and apply state changes efficiently.

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

The builder package is designed around a modular architecture that separates concerns between block construction, transaction execution, and state management. It interacts with the blockchain to retrieve the current chain head and state, uses an executor component to run transactions in the virtual machine, and maintains a preconfirmed block state that accumulates transactions and state updates before finalization.

The package handles the complexities of gas price calculations, class declarations, and state diffs, ensuring that blocks are built according to protocol rules and are ready for consensus submission.

### Component Overview

- **Builder**: The main orchestrator that initializes blocks, runs transactions, and finalizes blocks.
- **Executor**: Executes transactions against the current state using the virtual machine and applies state changes.
- **BuildState**: Represents the current state of the block being built, including the preconfirmed block and gas consumption.
- **BuildResult**: Contains the results of the build process, including the finalized block and simulation results.

## Core Components

### Builder (builder.go)

The Builder struct manages the block building process. It initializes preconfirmed blocks based on the current blockchain head, manages transaction execution, and finalizes blocks by committing them to the blockchain. It also handles retrieving revealed block hashes for protocol compliance.

Key files:
- builder.go - Main builder implementation managing block construction and finalization.

### Executor (executor.go)

The Executor interface and its implementation handle the execution of transactions within the block building process. It runs transactions through the virtual machine, manages declared contract classes, updates state diffs, and produces transaction receipts. It also finalizes the execution by simulating the block state.

Key files:
- executor.go - Transaction execution and state update logic.

### BuildState (state.go)

BuildState holds the current state of the block under construction, including the preconfirmed block, gas consumed, and the revealed block hash. It provides methods to clone and clear the state, facilitating safe manipulation during the build process.

Key files:
- state.go - Definition and management of the build state.

### BuildResult (result.go)

BuildResult encapsulates the outcome of the block building process. It includes the preconfirmed block, simulation results, and gas consumption metrics. It also provides a method to generate a proposal commitment used in consensus.

Key files:
- result.go - Build result structure and proposal commitment generation.

## Key Features

1. **Preconfirmed Block Initialization**
   - Initializes a new block based on the current chain head, setting up necessary fields and gas price calculations.

2. **Transaction Execution and State Updates**
   - Executes batches of transactions using the virtual machine, applies state diffs, and manages declared contract classes.

3. **Gas Consumption Tracking**
   - Tracks L2 gas consumed during transaction execution to support accurate fee calculations.

4. **Block Finalization**
   - Finalizes the block by simulating the state and preparing the block for consensus submission.

5. **Class Declaration Handling**
   - Supports processing and storage of declared contract classes during transaction execution.

6. **State Cloning and Management**
   - Provides cloning and clearing of build state to support safe concurrent operations and retries.

## Usage

### Basic Integration

To use the builder package, initialize a Builder instance with a blockchain and executor. Use the Builder to create a preconfirmed block, run transactions, and finalize the block.

### Configuration

The Builder requires dependencies such as the blockchain instance and an executor implementation. The executor itself requires a virtual machine instance and configuration flags for fee handling and validation.

## API Reference

### Types

#### Builder

Manages block building lifecycle including initialization, transaction execution, and finalization.

#### Executor

Interface defining methods to run transactions and finish execution.

#### BuildState

Represents the current state of the block being built, including preconfirmed block and gas consumption.

#### BuildResult

Contains the finalized block, simulation results, and gas usage metrics.

### Functions

#### New (Builder)

Creates a new Builder instance with the provided blockchain and executor.

#### Finalise

Finalizes a preconfirmed block by committing it to the blockchain with a signer and private key.

#### InitPreconfirmedBlock

Initializes a new preconfirmed block based on build parameters such as builder address, gas prices