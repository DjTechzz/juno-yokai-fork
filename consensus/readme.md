# Consensus Package

## Overview

The consensus package implements the Tendermint consensus protocol tailored for the Juno blockchain. It manages the process of proposing, voting, and committing blocks in a Byzantine fault-tolerant manner. The package integrates with the P2P networking layer to exchange consensus messages and persists state changes to enable recovery after failures.

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

The consensus package is designed around a modular architecture that separates concerns such as consensus logic, networking, block proposal, and state persistence. It uses a state machine to process incoming consensus messages and timeouts, producing actions that drive the consensus progress. The package interacts with the blockchain layer to commit finalized blocks and uses a write-ahead log (WAL) to ensure durability and recovery.

### Component Overview

- **Tendermint Engine**: Implements the core Tendermint consensus state machine and rules.
- **Consensus Driver**: Coordinates message handling, state machine execution, and action dispatch.
- **Block Proposer**: Builds and manages block proposals including transaction processing.
- **P2P Networking**: Handles message broadcasting and listening over the network using libp2p.
- **Database Layer**: Provides persistent storage for consensus messages and WAL entries.
- **Types**: Defines core data structures and message types used throughout the consensus process.

## Core Components

### Tendermint Engine (consensus/tendermint)

The Tendermint engine implements the consensus state machine, processing proposals, prevotes, precommits, and timeouts according to Tendermint rules. It manages the consensus state, validates messages, and generates actions such as broadcasting votes or committing blocks.

Key files:
- tendermint.go - Main Tendermint implementation
- process.go - Consensus process logic
- timeout.go - Timeout handling

### Consensus Driver (consensus/driver)

The consensus driver listens for network messages and timeouts, feeds them into the Tendermint state machine, and executes resulting actions. It manages scheduling of timeouts and coordinates committing blocks to the blockchain.

Key files:
- driver.go - Main driver implementation

### Block Proposer (consensus/proposer)

The block proposer builds new block proposals by processing transactions and interacting with the builder module. It manages the lifecycle of the preconfirmed block and handles re-running transactions after commits.

Key files:
- proposer.go - Proposer logic

### P2P Networking (consensus/p2p)

The P2P module manages consensus message broadcasting and listening using libp2p pubsub. It includes components for proposal streaming, vote handling, and message buffering to ensure efficient and reliable network communication.

Key components:
- p2p.go - Main P2P implementation
- broadcaster.go - Message broadcasting
- listener.go - Message listening

### Database Layer (consensus/db)

The database layer provides persistent storage for consensus messages and the write-ahead log (WAL). It batches writes to reduce disk I/O and supports replaying WAL entries to recover consensus state after restarts.

Key files:
- db.go - Database operations
- buckets_consensus.go - Storage buckets

### Types (consensus/types)

This package defines the core types used in consensus, including messages, proposals, votes, timeouts, and actions. It provides generic interfaces and concrete implementations for Starknet-specific types.

Key types:
- messages.go - Message types
- proposal.go - Proposal types
- state.go - State types

## Key Features

1. **Byzantine Fault Tolerance**
   - Implements Tendermint consensus to tolerate up to one-third faulty or malicious validators.

2. **Block Proposal and Voting**
   - Supports proposing blocks, prevoting, and precommitting with strict protocol rules.

3. **State Persistence**
   - Uses a write-ahead log to persist consensus messages and timeouts for crash recovery.

4. **P2P Communication**
   - Integrates with libp2p pubsub for efficient broadcasting and listening of consensus messages.

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
// Initialize dependencies
db := consensus.db.NewTendermintDB(...)
stateMachine := consensus.tendermint.New(...)
p2p := consensus.p2p.New(...)
proposer := consensus.proposer.New(...)

// Create consensus driver
driver := consensus.driver.New(log, db, stateMachine, blockchain, p2p, proposer, getTimeout)

// Run consensus loop
ctx := context.Background()
if err := driver.Run(ctx); err != nil {
    log.Fatal(err)
}
```

### Handling Consensus Messages

```go
// Process incoming proposal message
actions := stateMachine.ProcessProposal(proposalMessage)

// Execute resulting actions such as broadcasting votes
for _, action := range actions {
    switch a := action.(type) {
    case *types.BroadcastPrevote:
        p2p.Broadcasters().PrevoteBroadcaster.Broadcast(ctx, *a)
    case *types.BroadcastPrecommit:
        p2p.Broadcasters().PrecommitBroadcaster.Broadcast(ctx, *a)
    }
}
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