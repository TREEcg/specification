<img src="tree-logo.svg" alt="TREE logo" width="400px"/>

[Read the spec](https://w3id.org/tree/specification)

The ᴛʀᴇᴇ hypermedia specification enables data publishers and API developers to describe datasets as collections of entity descriptions (a set of quads), called “members”.
One or more views of this collection can be created.
A view allows organizing the members into multiple pages or nodes, and these nodes are interlinked using relations and/or search forms.
This way, a user agent that can interpret the ᴛʀᴇᴇ hypermedia controls can find the most efficient way to the members of interest.

## The W3C Community Group

This specification is managed by the [W3C ᴛʀᴇᴇ Community Group](https://www.w3.org/community/treecg/). You can join [the public mailing list](https://lists.w3.org/Archives/Public/public-treecg/) or [join one of the online meetings](https://calendar.google.com/calendar/u/0/embed?src=4b04fcfb0103a4f96d703b8b8255f15378d5f4729b9083bfc0129b676ae783d1@group.calendar.google.com&ctz=Europe/Brussels).

The goal of the community group in 2025 is to further evolve the spec in features and to become more testable. Releases are made after each call, listing the consensus that has been reached during the call.

In case of questions, feel free to contact the current chair [Pieter Colpaert](https://pietercolpaert.be).

## Developing

The specs are built using [Bikeshed](https://tabatkins.github.io/bikeshed/).
The following commands build each specification in development mode:

To build the TREE specification:
```bash
bikeshed watch ./01-tree-specification.bs
```
To build the Shape Topologies specification:
```bash
bikeshed watch ./02-shape-topologies.bs
```
To build the Discovery specification:
```bash
bikeshed watch ./03-discovery-specification.bs
```
The spec will be built [automatically](.github/workflows/) when pushing to the master branch.
