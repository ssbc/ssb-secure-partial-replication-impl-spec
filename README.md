# SSB secure partial replication implementation notes

This document consists of loose thoughts about how to structure an
implementation of [ssb-secure-partial-replication] in the JavaScript
stack.

## Components

### Meta feeds

A component for:
 - locally managing your meta feeds including create, query seed etc.

Links:
- spec: [ssb-meta-feeds-spec]
- implementation: [ssb-meta-feeds]
- Subset replication for getting the meta feed announce messages of
one or more feeds: [ssb-meta-feeds-rpc]

### Bendy butt

- Implementation: [ssb-bendy-butt]
- db2 support:
  - base: https://github.com/ssb-ngi-pointer/ssb-db2/pull/241
  - encryption: https://github.com/ssb-ngi-pointer/ssb-db2/pull/231

### Index writer

A component responsible for writing and updating the [index feeds].

Implementation: [ssb-index-feed-writer]

### Feed replicator

The purpose of this module is to do enable replication based on:
friends, meta feed support (indexes), available replication
posibilties of remote peers (EBT, subset replication, history stream?,
) and local configuration choices.

Local configuration could include things like: 
 - what hops to always do full replication
 - what hops to never do full replication even if partial replication
   is not available
   
This module needs some kind of status to see what it is doing. The
good thing about indexes is that it is natural points that has a
linear seq like old feeds and this means that we don't need to store
extra state like the potential browser-core starting point for this
module [feed-syncer].

Under here we also need to handle bendy butt over EBT (another EBT
instance as this is binary) and [getIndexFeed] RPC method for
efficiently getting an index feed including the linked messages:
[ssb-meta-feeds-rpc].

### Netsim tests

We should be able to use netsim to test all of this new stuff against
the existing and between implementations (go, js). Probably start with
a base case of ~100 peers and 100k messages and test: 10, 30, 90% meta
feeds.

### Example app

- meta feeds explorer
- browser demo (codename 8K)

### Fusion identity (Stretch goal)

[fusion identity spec]

A component for:
 - Doing operations on identities including inviting, attestation etc.
 - Query methods for the state

Should probably be written using ssb-crut:

- https://gitlab.com/ahau/ssb-crut/-/tree/db2

### Set replication (Stretch goal)

Implement the protocol described in https://hackmd.io/mPmWbNaDR9-AfrM1mSYtHQ

Maybe start with a simplified version

Related work:
 - [Automerge set replication]

### Network identity? (Stretch goal)

Maybe: https://github.com/ssb-ngi-pointer/ssb-secure-partial-replication-spec/pull/5#issuecomment-892221314

## DONE

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
[feed-syncer]: https://github.com/arj03/ssb-browser-core/blob/master/feed-syncer.js
[ssb-meta-feeds-spec]: https://github.com/ssb-ngi-pointer/ssb-meta-feed-spec
[ssb-meta-feeds]: https://github.com/ssb-ngi-pointer/ssb-meta-feeds
[ssb-meta-feeds-rpc]: https://github.com/ssb-ngi-pointer/ssb-meta-feeds-rpc
[ssb-bendy-butt]: https://github.com/ssb-ngi-pointer/ssb-bendy-butt
[ssb-index-feed-writer]: https://github.com/ssb-ngi-pointer/ssb-index-feed-writer
