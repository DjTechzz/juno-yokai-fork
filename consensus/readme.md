# Consensus Package

## Overview

The consensus package implements the core consensus mechanism for the Juno blockchain, based on the Tendermint consensus algorithm. It manages the proposal, voting, and commitment of blocks among validators in a Byzantine fault-tolerant manner. The package handles message processing, state transitions, timeout scheduling, and persistence to ensure reliable and consistent block finalization.

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

The consensus package is designed with modular components that interact to implement the Tendermint consensus protocol. It includes a state machine that processes consensus messages and timeouts, a driver that orchestrates message handling and action execution, a proposer module for block creation, and a P2P networking layer for message dissemination. The package also includes a database layer for write-ahead logging (WAL) to persist consensus state and enable recovery after crashes.

### Component Overview

- **Tendermint Engine**: Implements the consensus state machine and rules.
- **Consensus Driver**: Coordinates message reception, state machine processing, and action execution.
- **Block Proposer**: Builds and manages block proposals.
- **P2P Networking**: Handles message broadcasting and listening over the network.
- **Database Layer**: Provides persistent storage for consensus messages and timeouts.
- **Types**: Defines core data structures and message types used in consensus.

## Core Components

### Tendermint Engine (consensus/tendermint)

The Tendermint engine implements the consensus state machine, processing proposals, prevotes, precommits, and timeouts according to the Tendermint protocol rules. It manages the consensus state, validates messages, and generates actions such as broadcasting votes or committing blocks.

Key files:
- tendermint.go - Main Tendermint implementation
- process.go - Consensus process logic
- timeout.go - Timeout handling

### Consensus Driver (consensus/driver)

The consensus driver listens for incoming consensus messages from the network and timeouts, passes them to the Tendermint state machine for processing, and executes the resulting actions. It manages scheduling of timeouts and coordinates block commits with the blockchain and proposer modules.

Key files:
- driver.go - Main driver implementation

### Block Proposer (consensus/proposer)

The block proposer builds new block proposals by collecting transactions and interacting with the builder module. It manages the lifecycle of the preconfirmed block, handles transaction submission, and finalizes proposals for consensus.

Key files:
- proposer.go - Proposer logic

### P2P Networking (consensus/p2p)

The P2P networking layer manages message broadcasting and listening using libp2p pubsub. It includes components for proposal and vote broadcasting, proposal stream demultiplexing, and vote listeners. It ensures efficient and reliable message dissemination among validators.

Key components:
- p2p.go - Main P2P implementation
- broadcaster.go - Message broadcasting
- listener.go - Message listening

### Database Layer (consensus/db)

The database layer provides persistent storage for consensus messages and timeouts using a write-ahead log (WAL). It batches writes to reduce disk I/O and supports replaying WAL entries to recover consensus state after restarts.

Key files:
- db.go - Database operations
- buckets_consensus.go - Storage buckets

### Types (consensus/types)

This package defines the core types used throughout the consensus module, including message types, proposals, votes, timeouts, and consensus state representations.

Key types:
- messages.go - Message types
- proposal.go - Proposal types
- state.go - State types

## Key Features

1. **Byzantine Fault Tolerance**
   - Implements Tendermint consensus to tolerate up to one-third faulty or malicious validators while ensuring safety and liveness.

2. **Block Proposal and Voting**
   - Supports proposer selection, block proposal streaming, prevote and precommit voting phases, and final block commitment.

3. **State Persistence**
   - Uses a write-ahead log to persist consensus messages and timeouts, enabling recovery and preventing equivocation after crashes.

4. **P2P Communication**
   - Utilizes libp2p pubsub for efficient broadcasting and listening of consensus messages among validators.

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

Represents a block proposal including height, round, block data, and timestamp.

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