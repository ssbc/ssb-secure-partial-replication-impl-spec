<!--
SPDX-FileCopyrightText: 2021 Anders Rune Jensen

SPDX-License-Identifier: CC-BY-4.0
-->

# SSB-JS partial replication implementation notes

This document consists of loose thoughts about how to structure an
implementation of [ssb-secure-partial-replication] in the JavaScript
stack.

## Components

### Meta feeds

- spec: [ssb-meta-feeds-spec]
- implementation: [ssb-meta-feeds]

- validation: https://github.com/ssb-ngi-pointer/ssb-meta-feeds/pull/18
- random test failures: https://github.com/ssb-ngi-pointer/ssb-meta-feeds/issues/19
- box2 support https://github.com/ssb-ngi-pointer/ssb-meta-feeds/pull/25

### Bendy butt / DB2

- Implementation: [ssb-bendy-butt]

- db2 support:
  - base: https://github.com/ssb-ngi-pointer/ssb-db2/pull/252
  - encryption: https://github.com/ssb-ngi-pointer/ssb-db2/pull/253

### RPC/EBT changes

- EBT changes: multiple streams & getClock:
  https://github.com/cryptoscope/ssb/pull/111
- Remove resolvedIndexFeed: [ssb-meta-feeds-rpc]
- type attack: https://github.com/ssb-ngi-pointer/ssb-meta-feeds-rpc/issues/9

### Feed replicator

https://github.com/ssb-ngi-pointer/ssb-replication-scheduler/pull/5

### Netsim tests

We should be able to use netsim to test all of this new stuff against
the existing and between implementations (go, js). Probably start with
a base case of ~100 peers and 100k messages and test: 10, 30, 90% meta
feeds.

### Example app

- meta feeds explorer
- DONE browser demo https://github.com/ssb-ngi-pointer/8k-demo

### Fusion identity (Stretch goal)

[fusion identity spec]

A component for:
 - Doing operations on identities including inviting, attestation etc.
 - Query methods for the state

Should probably be written using ssb-crut:

- https://gitlab.com/ahau/ssb-crut/-/tree/db2

### Set replication (Stretch goal)

Implement the protocol described in Aljoscha's master thesis. Maybe
start with a simplified version

Related work:
 - [Automerge set replication]

### Network identity? (Stretch goal)

https://github.com/ssb-ngi-pointer/ssb-network-identity-spec

## DONE

### Index writer

A component responsible for writing and updating the [index feeds].

Implementation: [ssb-index-feed-writer]

### Netsim

A [network simulator] that supports JS & go nodes.

### Fixed SSB-ebt

A EBT component that only takes care of EBT replication for a given
subset of feeds. Could start with [simple-ebt].

### SSB-friends

SSB friends has been untangled from ssb-replicate and should be run in
that mode.

[ssb-secure-partial-replication]: https://github.com/ssb-ngi-pointer/ssb-secure-partial-replication
[ssb-subset-replication]: https://github.com/ssb-ngi-pointer/ssb-subset-replication
[trustnet]: https://github.com/cblgh/trustnet
[ssb-fixtures]: https://github.com/ssb-ngi-pointer/ssb-fixtures/
[box2 DM]: https://github.com/ssbc/private-group-spec/blob/master/direct-messages/README.md
[fusion identity spec]: https://github.com/ssb-ngi-pointer/fusion-identity-spec
[network simulator]: https://github.com/ssb-ngi-pointer/netsim
[index feeds]: https://github.com/ssb-ngi-pointer/ssb-secure-partial-replication#indexes
[Automerge set replication]: https://github.com/automerge/automerge/blob/c0376c0d9f0bdd6d8445edb34c68e2abe4bdf3fd/backend/sync.js
[getindexfeed]: https://github.com/ssb-ngi-pointer/ssb-subset-replication#getindexfeedfeedid-source
[getSubset]: https://github.com/ssb-ngi-pointer/ssb-subset-replication#getsubsetquery-options-source

[simple-ebt]: https://github.com/arj03/ssb-browser-core/blob/master/simple-ebt.js
[feed-syncer]: https://github.com/arj03/ssb-browser-core/blob/master/feed-replication.js
[ssb-meta-feeds-spec]: https://github.com/ssb-ngi-pointer/ssb-meta-feed-spec
[ssb-meta-feeds]: https://github.com/ssb-ngi-pointer/ssb-meta-feeds
[ssb-meta-feeds-rpc]: https://github.com/ssb-ngi-pointer/ssb-meta-feeds-rpc
[ssb-bendy-butt]: https://github.com/ssb-ngi-pointer/ssb-bendy-butt
[ssb-index-feed-writer]: https://github.com/ssb-ngi-pointer/ssb-index-feed-writer
