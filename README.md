# SSB secure partial replication implementation notes

This document consists of loose thoughts about how to structure an
implementation of [ssb-secure-partial-replication] in the JavaScript
stack.

## Components

### Meta feed

A component for:
 - locally managing your meta feeds including create, seed values etc.
 - the ability to reason about feeds of another identity and to use
   that for replication

Implementation of spec: https://github.com/ssb-ngi-pointer/ssb-meta-feed-spec
JS implementation: https://github.com/ssb-ngi-pointer/ssb-meta-feeds

### Index writer

A component responsible for writing and updating the index feeds.

See https://github.com/ssb-ngi-pointer/ssb-secure-partial-replication#indexes

### Netsim

A network simulator that supports JS & go nodes

https://github.com/ssb-ngi-pointer/netsim

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
module
[feed-syncer](https://github.com/arj03/ssb-browser-core/blob/master/feed-syncer.js).

```
initial sync:

get own messages
for feeds in hops 1: get feeds in full
for feeds in hops 2:
 - get metafeed/announce messages
 - for those that have, get metafeed (hydrate), then get index feeds (hydrate),
   if exists then we can do partial
```

### Subset query

Implement the subset RPC method described in
https://github.com/ssb-ngi-pointer/ssb-subset-replication#subsetquery-options-source

### Fixed SSB-ebt

A EBT component that only takes care of EBT replication for a given
subset of feeds. Could start with
[simple-ebt](https://github.com/arj03/ssb-browser-core/blob/master/simple-ebt.js).

### SSB-friends

SSB friends has been untangled from ssb-replicate and should be run in
that mode.

### Feedless/fusion identity

Repo: https://github.com/ssb-ngi-pointer/fusion-identity-spec

Module that interprets the feedless identity purpose feeds and
presents an interface to query this. We probably should use [box2 DM]
for private messages to these identities.

### Subset replication

[ssb-subset-replication] as a more general createhistorystream.

This might be quite important for getting metafeed/announce messages
of feeds in hops 2 in a fast way.

### Trustnet

The general [trustnet] algorithm for liquid democrazy style trust
delegation

### Claims writer

A component responsible for writing claims to the meta feed. By
default it should just write claims for its own main feed when
configured.

### Claims auditor

A component responsible for validating claims. Uses trustnet and by
default should create audits for claims with less than 3 existing
audits within hops 2. One interesting problem will be to make sure
that no peers in the network will end up with a too large portion of
the audits.

### Set replication

Implement the protocol described in https://hackmd.io/mPmWbNaDR9-AfrM1mSYtHQ

Maybe start with a simplified version

Related work:
 -Auto merge set replication: https://github.com/automerge/automerge/blob/c0376c0d9f0bdd6d8445edb34c68e2abe4bdf3fd/backend/sync.js


[ssb-secure-partial-replication]: https://github.com/ssb-ngi-pointer/ssb-secure-partial-replication
[ssb-subset-replication]: https://github.com/ssb-ngi-pointer/ssb-subset-replication
[trustnet]: https://github.com/cblgh/trustnet
[ssb-fixtures]: https://github.com/ssb-ngi-pointer/ssb-fixtures/
[box2 DM]: https://github.com/ssbc/private-group-spec/blob/master/direct-messages/README.md
