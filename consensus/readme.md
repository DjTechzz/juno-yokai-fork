# Consensus Package

## Overview

The consensus package implements the Tendermint consensus algorithm tailored for the Juno blockchain. It manages the process of proposing, voting, and committing blocks in a Byzantine fault-tolerant manner. The package integrates with the P2P networking layer to broadcast and receive consensus messages, maintains persistent state through a write-ahead log, and coordinates with the blockchain application to finalize blocks.

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

The consensus package is designed around a modular architecture that separates concerns such as consensus logic, networking, block proposing, and state persistence. At its core, the Tendermint engine drives the consensus state machine, processing proposals, prevotes, and precommits. The consensus driver orchestrates message handling and timeout scheduling. The P2P networking layer handles message broadcasting and listening, while the proposer component manages block building and transaction inclusion. Persistent state is maintained in a dedicated database layer to ensure recovery and fault tolerance.

### Component Overview

- **Tendermint Engine**: Implements the core consensus state machine and rules.
- **Consensus Driver**: Coordinates message processing, timeout handling, and state transitions.
- **Block Proposer**: Builds blocks by collecting transactions and finalizing proposals.
- **P2P Networking**: Manages broadcasting and receiving consensus messages over the network.
- **Database Layer**: Provides persistent storage for consensus state and write-ahead logs.
- **Types**: Defines core data structures and message types used throughout consensus.

## Core Components

### Tendermint Engine (consensus/tendermint)

The Tendermint engine implements the consensus state machine, processing proposals, votes, and timeouts according to Tendermint rules. It maintains internal state, validates messages, and produces actions such as broadcasting votes or committing blocks.

Key files:
- tendermint.go - Main Tendermint implementation
- process.go - Consensus process logic
- timeout.go - Timeout handling

### Consensus Driver (consensus/driver)

The consensus driver listens for incoming consensus messages and timeouts, passing them to the Tendermint state machine. It executes resulting actions like broadcasting messages or committing blocks, coordinating between the state machine, blockchain, proposer, and P2P layers.

Key files:
- driver.go - Main driver implementation

### Block Proposer (consensus/proposer)

The block proposer manages building blocks by collecting transactions, running them through the builder, and finalizing proposals. It interacts with the Tendermint application interface to provide valid proposal values and handles re-running transactions after commits.

Key files:
- proposer.go - Proposer logic

### P2P Networking (consensus/p2p)

The P2P networking component handles broadcasting and listening for consensus messages using libp2p pubsub. It includes broadcasters for proposals and votes, listeners for incoming messages, and a proposal stream demultiplexer to manage concurrent proposal streams.

Key components:
- p2p.go - Main P2P implementation
- broadcaster.go - Message broadcasting
- listener.go - Message listening

### Database Layer (consensus/db)

The database layer provides persistent storage for the Tendermint write-ahead log (WAL) and consensus state. It batches writes to reduce disk I/O and supports replaying WAL entries to recover consensus state after restarts.

Key files:
- db.go - Database operations
- buckets_consensus.go - Storage buckets

### Types (consensus/types)

This package defines core types used in consensus messages and state, including proposals, votes, timeouts, and actions. It provides generic interfaces and implementations tailored for Starknet and Juno.

Key types:
- messages.go - Message types
- proposal.go - Proposal types
- state.go - State types

## Key Features

1. **Byzantine Fault Tolerance**
   - Implements Tendermint consensus to tolerate up to one-third faulty or malicious validators while ensuring safety and liveness.

2. **Block Proposal and Voting**
   - Supports proposing blocks, prevoting, and precommitting with strict rules to reach consensus on the next block.

3. **State Persistence**
   - Uses a write-ahead log to persist consensus messages and timeouts, enabling recovery after crashes without losing consensus progress.

4. **P2P Communication**
   - Integrates with libp2p pubsub for efficient broadcasting and receiving of consensus messages among validators.

## Usage

### Basic Integration

```go
// Example code showing basic usage
import "github.com/NethermindEth/juno/consensus"

// Initialize consensus
consensus := consensus.New(config)

// Start consensus
consensus.Start()
```

### Configuration

```go
// Example configuration
type Config struct {
    ValidatorAddress string
    NetworkConfig    NetworkConfig
    DBPath           string
}
```

## API Reference

### Main Types

#### ConsensusState

```go
type ConsensusState struct {
    Height    uint64
    Round     uint32
    Step      Step
    BlockHash []byte
}
```

Represents the current state of the consensus engine including height, round, step, and the hash of the locked block.

#### Proposal

```go
type Proposal struct {
    Height    uint64
    Round     uint32
    BlockData []byte
    Timestamp time.Time
}
```

Represents a block proposal message containing the block data and metadata for a given height and round.

### Key Functions

#### NewConsensus

```go
func NewConsensus(config Config) *Consensus
```

Creates a new consensus instance.

Parameters:
- config: Configuration object

Returns:
- *Consensus: New consensus instance

#### Start

```go
func (c *Consensus) Start() error
```

Starts the consensus engine.

Returns:
- error: Any error that occurred

## Examples

### Starting a Validator Node

```go
// Example code here
```

### Handling Consensus Messages

```go
// Example code here
```

## Contributing

To contribute to the consensus package:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

## License

This project is licensed under the Apache License 2.0. See the LICENSE file for details.