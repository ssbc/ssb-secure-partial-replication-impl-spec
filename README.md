# SSB secure partial replication implementation notes

This document consists of loose thoughts about how to structure an
implementation of [ssb-secure-partial-replication] in the JavaScript
stack.

## Components

### Meta feeds

A component for:
 - locally managing your meta feeds including create, seed values etc.
 - the ability to reason about feeds of another identity and to use
   that for replication

Links:
- [ssb-meta-feeds-spec]
- JS implementation: [ssb-meta-feeds]
- Subset replication for getting the meta feed announce messages of
one or more feeds: [ssb-meta-feeds-rpc]

### Bendy butt

- Implementaiton of feed format: [ssb-bendy-butt]
- db2 support: https://github.com/ssb-ngi-pointer/ssb-db2/pull/231

### Index writer

A component responsible for writing and updating the [index feeds].

[getIndexFeed] RPC method for efficiently getting an index feed
including the linked messages: [ssb-meta-feeds-rpc]

### SSB feed replicator

The purpose of this module is to figure out how to replicate a
specific identity, by taking into account: meta feed support
(indexes), claims, available replication posibilties of remote peers
(EBT, history stream, subset replication) and local configuration
choices.

Local configuration could include things like: 
 - what hops to always do full replication
 - what hops to never do full replication even if partial replication
   is not available
   
This module needs some kind of status to see what it is doing. The
good thing about indexes is that it is natural points that has a
linear seq like old feeds and this means that we don't need to store
extra state like the potential browser-core starting point for this
module [feed-syncer].

```
initial sync:

get own messages
for feeds in hops 1: get feeds in full
for feeds in hops 2:
 - get metafeed/announce messages using [getSubset] RPC
 - for those that have, get metafeed (hydrate), then get index feeds (hydrate),
   if exists then we can do partial
```

### Fusion identity

[fusion identity spec]

A component for:
 - Doing operations on identities including inviting, attestation etc.
 - Query methods for the state

Should probably be written using https://github.com/arj03/ssb-crut/

### Trustnet

The general [trustnet] algorithm for liquid democrazy style trust
delegation.

#### Claims

Indexing of data in other meta feeds. Should use the same index writer
and RPC as indexes for replication.

#### Claims auditor

A component responsible for validating claims. Uses trustnet and by
default should create audits for claims with less than 3 existing
audits within hops 2. One interesting problem will be to make sure
that no peers in the network will end up with a too large portion of
the audits.

### Set replication

Implement the protocol described in https://hackmd.io/mPmWbNaDR9-AfrM1mSYtHQ

Maybe start with a simplified version

Related work:
 - [Automerge set replication]

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
