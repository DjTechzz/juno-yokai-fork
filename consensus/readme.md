# Consensus Package

## Overview

The consensus package implements the core consensus mechanism for the Juno blockchain, based on the Tendermint consensus algorithm. It manages the process of proposing, voting, and committing blocks in a Byzantine fault-tolerant manner. The package integrates with the P2P networking layer to broadcast and receive consensus messages, and persists state changes to ensure recovery after failures.

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

The consensus package is designed around a modular architecture that separates concerns such as consensus logic, networking, block proposing, and state persistence. It uses a state machine to process consensus messages and timeouts, and a driver to coordinate message handling and action execution. The P2P layer handles message broadcasting and listening, while the database layer manages durable storage of consensus state and events.

### Component Overview

- **Tendermint Engine**: Implements the Tendermint consensus state machine and rules.
- **Consensus Driver**: Coordinates message processing, action execution, and timeout scheduling.
- **Block Proposer**: Builds and submits block proposals based on transactions.
- **P2P Networking**: Manages message broadcasting and listening over libp2p.
- **Database Layer**: Provides persistent storage for consensus messages and WAL entries.
- **Types**: Defines core types for messages, proposals, votes, and consensus state.

## Core Components

### Tendermint Engine (consensus/tendermint)

The Tendermint engine implements the consensus state machine, processing proposals, prevotes, precommits, and timeouts according to Tendermint rules. It maintains internal state, validates messages, and generates actions such as broadcasting votes or committing blocks.

Key files:
- tendermint.go - Main Tendermint implementation
- process.go - Consensus process logic
- timeout.go - Timeout handling

### Consensus Driver (consensus/driver)

The driver listens for incoming consensus messages and timeouts, passes them to the state machine for processing, and executes resulting actions. It handles broadcasting messages, scheduling timeouts, committing blocks, and managing the WAL.

Key files:
- driver.go - Main driver implementation

### Block Proposer (consensus/proposer)

The proposer builds new block proposals by collecting transactions and interacting with the builder module. It manages the lifecycle of the preconfirmed block, submits proposals to Tendermint, and handles commits.

Key files:
- proposer.go - Proposer logic

### P2P Networking (consensus/p2p)

The P2P package manages consensus message broadcasting and listening using libp2p pubsub. It includes components for proposal streaming, vote broadcasting, and message demultiplexing to handle concurrent proposal streams.

Key components:
- p2p.go - Main P2P implementation
- broadcaster.go - Message broadcasting
- listener.go - Message listening

### Database Layer (consensus/db)

The database layer provides persistent storage for consensus messages and the write-ahead log (WAL). It batches writes for efficiency and supports replaying WAL entries to recover consensus state after restarts.

Key files:
- db.go - Database operations
- buckets_consensus.go - Storage buckets

### Types (consensus/types)

Defines core types used throughout the consensus package, including messages, proposals, votes, timeouts, and consensus state identifiers.

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
   - Uses a write-ahead log (WAL) to persist consensus messages and timeouts, enabling recovery after crashes without losing consensus progress.

4. **P2P Communication**
   - Integrates with libp2p to broadcast and listen for consensus messages efficiently, supporting multiple concurrent proposal streams.

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

Represents a block proposal message containing the block data and metadata.

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
// Initialize components
db := db.NewTendermintDB(...)
stateMachine := tendermint.New(...)
p2p := p2p.New(...)
proposer := proposer.New(...)

driver := driver.New(logger, db, stateMachine, blockchain, p2p, proposer, timeoutFunc)

ctx := context.Background()
go p2p.Run(ctx)
go proposer.Run(ctx)
err := driver.Run(ctx)
if err != nil {
    log.Fatal(err)
}
```

### Handling Consensus Messages

```go
// Example of processing a received proposal message
actions := stateMachine.ProcessProposal(proposal)
for _, action := range actions {
    switch a := action.(type) {
    case *types.BroadcastPrevote:
        p2p.Broadcasters().PrevoteBroadcaster.Broadcast(ctx, *a)
    case *types.Commit:
        blockchain.Commit(a.Height, *a.Value)
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

This package is licensed under the Apache License 2.0. See the LICENSE file for details.