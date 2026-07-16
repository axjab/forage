# forage
A Unix-inspired shopping list backend that feels like TaskWarrior.

## Design Philosophy
- KISS.
- Unix philosophy.
- TaskWarrior-inspired UX.
- RESTful backend.
- Interface-agnostic (CLI, TUI, SMS, WhatsApp, REST, etc.).

- ## Terminology
| Concept                  | Name         |
| ------------------------ | ------------ |
| User                     | **forager**  |
| Shopping list            | **list**     |
| Item                     | **item**     |
| Store                    | **merchant** |
| Arbitrary extension data | **xprops**   |

## Recurrence

Recurrence belongs to the list-item relationship, not the global item.
That means:

One list can restock milk every week.
Another list can use the same milk item without recurrence.
No global side effects.

## Item Lifecycle
When an item is deleted, it is not immediately removed from lists, the system is designed to clean itself up naturally.
```
create
    │
    ▼
active
    │
delete
    ▼
deleted
(hidden from searches)
    │
prune
(removes from lists)
    ▼
if referenced by zero lists
    │
    ▼
hard delete
```

## Relationship Model
```
Forager
    │
    └──── owns ────► List

List
    │
    └────< ListItem >──── Item

Item
    │
    └────< ItemMerchant >──── Merchant
```
