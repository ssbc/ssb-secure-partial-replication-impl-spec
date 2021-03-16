# SSB secure partial replication implementation notes

This document consists of loose thoughts about how to structure an
implementation of [ssb-secure-partial-replication] in the JavaScript
stack.

## Components

### Trustnet

The general [trustnet] algorithm for liquid democrazy style trust delegation

### Claims writer

A component responsible for writing claims to the meta feed. By
default it should just write claims for its own main feed when
configured.

### Claims auditor

A component responsible for validating claims. Uses trustnet and by
default should create audits for claims with less than 3 existing
audits within hops 2.

### Subset replication

[ssb-subset-replication] will become a more general
createhistorystream. The set replication part is still undecided.

### SSB feed replicator

The purpose of this module is to figure out how to replicate a
specific feed, by taking into account: claims, available replication
posibilties of remote peers (EBT, history stream, subset replication)
and local configuration choices.

Local configuration could include things like: 
 - what hops to always do full replication
 - what hops to never do full replication even if partial replication
   is not available
   
This module needs some kind of status to see what it is doing.

One starting point for this module would be
[feed-syncer](https://github.com/arj03/ssb-browser-core/blob/master/feed-syncer.js).

### Fixed ssb-ebt

A EBT component that only takes care of EBT replication for a given
subset of feeds. Could start with
[simple-ebt](https://github.com/arj03/ssb-browser-core/blob/master/simple-ebt.js).

### ssb-friends

SSB friends has been untangled from ssb-replicate and should be run in
that mode.


[ssb-secure-partial-replication]: https://github.com/ssb-ngi-pointer/ssb-secure-partial-replication
[ssb-subset-replication]: https://github.com/ssb-ngi-pointer/ssb-subset-replication
[trustnet]: https://github.com/cblgh/trustnet
