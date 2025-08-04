# Consensus Package

## Overview

The consensus package implements the Tendermint consensus algorithm tailored for the Juno blockchain. It manages the process of proposing, voting, and committing blocks in a Byzantine fault-tolerant manner. The package integrates with the P2P network to exchange consensus messages and persists state changes to ensure recovery after failures.

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

The consensus package is designed around a modular architecture that separates concerns such as consensus logic, networking, block proposing, and state persistence. It uses a state machine to process consensus messages and timeouts, coordinating with the blockchain and P2P layers. The package also includes mechanisms for replaying consensus state from a write-ahead log to recover from crashes.

### Component Overview

- **Tendermint Engine**: Implements the core Tendermint consensus state machine and rules.
- **Consensus Driver**: Coordinates message handling, state machine execution, and action dispatch.
- **Block Proposer**: Builds and manages block proposals, including transaction processing.
- **P2P Networking**: Handles message broadcasting and listening over the network using libp2p.
- **Database Layer**: Provides persistent storage for consensus messages and timeouts.
- **Types**: Defines core data structures and message types used throughout the consensus process.

## Core Components

### Tendermint Engine (consensus/tendermint)

The Tendermint engine contains the consensus state machine that processes proposals, prevotes, precommits, and timeouts. It implements the Tendermint consensus rules and manages the internal consensus state.

Key files:
- tendermint.go - Main Tendermint implementation
- process.go - Consensus process logic
- timeout.go - Timeout handling

### Consensus Driver (consensus/driver)

The driver listens for incoming consensus messages and timeouts, passes them to the state machine, and executes resulting actions such as broadcasting messages or committing blocks. It acts as the main orchestrator of the consensus flow.

Key files:
- driver.go - Main driver implementation

### Block Proposer (consensus/proposer)

The proposer builds new block proposals by processing transactions and interacting with the builder component. It manages the lifecycle of proposals and handles commits to update its state.

Key files:
- proposer.go - Proposer logic

### P2P Networking (consensus/p2p)

This component manages peer-to-peer communication for consensus messages. It includes broadcasters for proposals and votes, listeners for incoming messages, and a demultiplexer for handling multiple concurrent proposal streams.

Key components:
- p2p.go - Main P2P implementation
- broadcaster.go - Message broadcasting
- listener.go - Message listening

### Database Layer (consensus/db)

The database layer provides persistent storage for consensus messages and timeouts using a write-ahead log (WAL). It supports batching writes and replaying state after restarts to ensure consensus continuity.

Key files:
- db.go - Database operations
- buckets_consensus.go - Storage buckets

### Types (consensus/types)

Defines the core types used in consensus messages, proposals, votes, timeouts, and actions. These types are generic to support different blockchain implementations.

Key types:
- messages.go - Message types
- proposal.go - Proposal types
- state.go - State types

## Key Features

1. **Byzantine Fault Tolerance**
   - Implements Tendermint consensus to tolerate up to one-third faulty or malicious validators.

2. **Block Proposal and Voting**
   - Supports proposing blocks, prevoting, and precommitting with strict protocol rules to ensure safety and liveness.

3. **State Persistence**
   - Uses a write-ahead log to persist consensus messages and timeouts, enabling recovery after crashes.

4. **P2P Communication**
   - Integrates with libp2p for efficient broadcasting and listening of consensus messages among validators.

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

Represents the current state of the consensus engine including height, round, step, and the hash of the proposed block.

#### Proposal

```go
type Proposal struct {
    Height    uint64
    Round     uint32
    BlockData []byte
    Timestamp time.Time
}
```

Represents a block proposal message containing the height, round, block data, and timestamp.

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