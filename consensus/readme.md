# Consensus Package

## Overview

The consensus package implements the core consensus logic for the Juno blockchain, providing a robust, crash-recoverable, and Byzantine Fault Tolerant (BFT) protocol based on Tendermint. It manages block proposal, voting, and finalization among validators, ensuring that all honest nodes agree on the same sequence of blocks. The package includes modules for networking, persistent state, proposer logic, and message types, enabling seamless integration with the rest of the Juno system.

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

The consensus package is organized into modular components that interact to achieve distributed agreement on the blockchain state. At its core, the Tendermint engine coordinates the consensus process, handling proposals, votes, and timeouts. The consensus driver orchestrates the flow of messages and actions between the state machine, networking, and block proposer. P2P networking ensures reliable message propagation among validators, while the database layer provides persistent storage for consensus-critical data. Types and message definitions ensure consistent communication and state representation across the system.

### Component Overview

- **Tendermint Engine**: Implements the consensus state machine and logic for block finalization.
- **Consensus Driver**: Manages the main event loop, integrating state machine, networking, and proposer.
- **Block Proposer**: Handles block creation and submission to the consensus process.
- **P2P Networking**: Provides message broadcasting, listening, and peer management.
- **Database Layer**: Persists consensus messages and state for crash recovery.
- **Types**: Defines consensus messages, proposals, votes, and state structures.

## Core Components

### Tendermint Engine (consensus/tendermint)

Implements the Tendermint BFT consensus algorithm, managing the state machine for proposals, prevotes, precommits, and timeouts. It ensures validators reach agreement on new blocks and handles crash recovery by replaying persisted messages.

Key files:
- tendermint.go - Main Tendermint implementation
- process.go - Consensus process logic
- timeout.go - Timeout handling

### Consensus Driver (consensus/driver)

Coordinates the consensus process by running the main event loop. It listens for network messages, triggers state transitions, executes actions from the state machine, and manages timeouts and block commits.

Key files:
- driver.go - Main driver implementation

### Block Proposer (consensus/proposer)

Responsible for building new block proposals, managing transaction pools, and submitting proposals to the consensus engine. Handles transaction batching and block finalization.

Key files:
- proposer.go - Proposer logic

### P2P Networking (consensus/p2p)

Handles peer-to-peer communication for consensus messages. Provides broadcasting and listening for proposals, votes, and other consensus-related messages. Integrates with libp2p for network transport.

Key components:
- p2p.go - Main P2P implementation
- broadcaster.go - Message broadcasting
- listener.go - Message listening

### Database Layer (consensus/db)

Provides persistent storage for consensus messages and state using a key-value store. Ensures crash recovery by storing all events that may change consensus state.

Key files:
- db.go - Database operations
- buckets_consensus.go - Storage buckets

### Types (consensus/types)

Defines the core types used in consensus, including proposals, votes, state, and messages. Ensures consistent serialization and deserialization across the system.

Key types:
- messages.go - Message types
- proposal.go - Proposal types
- state.go - State types

## Key Features

1. **Byzantine Fault Tolerance**
   
   The consensus engine tolerates up to one third of faulty or malicious validators, ensuring safety and liveness as long as a supermajority of nodes are honest.

2. **Block Proposal and Voting**
   
   Implements a multi-step process where validators propose blocks, exchange votes (prevote and precommit), and finalize blocks only when a quorum is reached.

3. **State Persistence**
   
   All consensus-critical events are persisted to disk, allowing nodes to recover their state and resume consensus after crashes or restarts.

4. **P2P Communication**
   
   Uses a peer-to-peer network to broadcast and receive consensus messages, ensuring reliable and timely propagation among validators.

## Usage

### Basic Integration

```go
import "github.com/NethermindEth/juno/consensus"

// Initialize consensus
consensus := consensus.New(config)

// Start consensus
consensus.Start()
```

### Configuration

```go
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

Represents the current state of the consensus process, including height, round, step, and the hash of the proposed block.

#### Proposal

```go
type Proposal struct {
    Height    uint64
    Round     uint32
    BlockData []byte
    Timestamp time.Time
}
```

Represents a block proposal, including the height, round, block data, and timestamp.

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
import (
    "github.com/NethermindEth/juno/consensus"
)

func main() {
    config := consensus.Config{
        ValidatorAddress: "0x123...",
        NetworkConfig:    myNetworkConfig,
        DBPath:           "/var/lib/juno/consensus.db",
    }
    node := consensus.NewConsensus(config)
    if err := node.Start(); err != nil {
        panic(err)
    }
}
```

### Handling Consensus Messages

```go
// Example handler for incoming consensus messages
func handleMessage(msg consensus.Message) {
    switch msg.Type {
    case consensus.MessageTypeProposal:
        // Process proposal
    case consensus.MessageTypePrevote:
        // Process prevote
    case consensus.MessageTypePrecommit:
        // Process precommit
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

[License information]