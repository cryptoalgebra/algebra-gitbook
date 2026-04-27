# VirtualTickStructure

Algebra tick structure abstract contract

Encapsulates the logic of interaction with the data structure with ticks

_Developer note: Ticks are stored as a doubly linked list. A two-layer bitmap tree is used to search through the list_

## Variables

### mapping(int24 => struct VirtualTickManagement.Tick) ticks
