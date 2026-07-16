[Return to README](https://github.com/axjab/forage/blob/master/README.md)

---
You are an expert PocketBase architect, REST API designer, and database modeller.

Your task is to design the backend model for an application called "Forage".

Forage is a minimalist, TaskWarrior-inspired shopping list backend. The interface is irrelevant. It may be accessed through a CLI, TUI, WhatsApp, SMS, REST client, or any other interface.

The backend must follow these principles:

- Unix philosophy.
- Keep it simple.
- Deterministic behavior.
- Low cognitive load.
- Human-friendly naming.
- REST-first design.
- Prefer explicit state over magic behavior.
- Prefer fewer collections over generalized architectures.
- Prefer JSON extension fields over creating new collections.
- Do not invent enterprise abstractions.

IMPORTANT:
This is NOT an inventory system.
This is NOT an e-commerce platform.
This is NOT an ERP.
Do not introduce warehouses, stock management, product variants, pricing engines, purchase orders, ACL systems, roles, permissions, event sourcing, CQRS, DDD layers, or unnecessary abstraction unless explicitly requested.

When multiple designs are possible, choose the simplest design that satisfies the requirements.

---

## Technology Constraint

The backend uses PocketBase.

PocketBase collections support:

- text
- rich text
- number
- boolean
- email
- URL
- datetime
- autodate
- file
- relation
- select
- JSON
- geo point

All collections must use lowercase names prefixed with:

forage_

because they coexist with unrelated PocketBase collections.

---

# Domain Vocabulary

Use these names:

User → Forager

Store/business → Merchant

Custom extension properties → xprops

Application name → Forage

---

# Required Collections

The design should revolve around these collections:

forage_foragers

forage_items

forage_lists

forage_list_items

forage_merchants

forage_item_merchants


Do not add additional collections unless you can clearly justify why they are required.

---

# Core Domain Rules

## Foragers

A forager is identified by phone number.

Authentication is out of scope.

A forager can own multiple lists.

---

## Lists

A list:

- always has one or more foragers associated with it
- may have multiple owners/authors
- can be renamed
- can be archived
- can be deleted
- can exist empty
- names do not need to be globally unique
- IDs must be unique

Ownership means:

"If a phone number is associated with a list, that forager may view and edit the list."

No roles.
No ACL.
No permissions model.

---

## Items

Items are reusable.

Example:

The item:

"milk"

may appear in:

- Family groceries
- Camping trip
- Holiday shopping

Removing an item from a list must not remove the item from the system.

An item may contain:

- id
- text
- quantity
- priority
- tags
- notes JSON
- xprops JSON
- created
- updated
- deletion state

Duplicates are allowed.

Do not merge duplicates automatically.

---

## List Items

The relationship between lists and items is where list-specific state belongs.

A list item may contain:

- list relation
- item relation
- checked state
- quantity override if needed
- priority override if needed
- ordering position
- notes
- recurrence information

The item itself must not know whether it is checked in a list.

---

# Quantity

Avoid ambiguity.

Use structured quantity:

Example:

{
  "qty": 5,
  "unit": "kg"
}

Do not parse natural language quantities.

---

# Priority

Use the inverse TaskWarrior-inspired scale:

0 = highest priority

9 = lowest priority

Default:

middle value.

User-friendly aliases may exist at the command layer.

---

# Recurrence

Recurrence belongs to the list-item relationship.

Do NOT attach recurrence directly to the item.

Reason:

The same item may recur differently depending on the list.

Use a JSON structure similar to:

{
  "active": true,
  "pattern": "0 0 * * *",
  "next_restock": "datetime"
}

When recurrence triggers:

- update next_restock
- if the list item is checked, clear/reset the checked state according to the implementation rules

---

# Merchants

Merchants are businesses associated with items.

Examples:

- Costco
- Canadian Tire
- Home Depot

The relationship is many-to-many:

Many merchants have many items.

Do not create unnecessary merchant-location/product tables.

Any optional merchant information belongs in JSON/xprops.

Possible information:

- address
- coordinates
- opening hours
- URL
- SKU
- prices
- notes

Do not normalize these unless there is a strong reason.

---

# Tags

Use hierarchical dot notation.

Example:

food.dairy

source.loblaws

hardware.tools

Avoid multiple tag syntaxes.

---

# Notes and xprops

Use JSON.

notes:

Human notes about the item.

Example:

[
  "only buy when discounted",
  "lactose free"
]

xprops:

Extensible properties belonging to the item.

Examples:

{
  "SKU": "12345",
  "brand": "Example",
  "average_cost": 4.99,
  "url": "..."
}

Do not create schema fields for every possible property.

---

# Deletion Model

Deletion is soft deletion.

Deleting an item:

- marks the item deleted
- hides it from normal searches/results
- does not immediately remove it from lists

Lists preserve the reference until:

prune

After pruning:

If the deleted item has zero remaining list references:

hard delete it.

Deleted items cannot be edited.

Only restoration or pruning operations apply.

---

# Command Philosophy

The command system should feel inspired by TaskWarrior.

Commands are:

- deterministic
- scriptable
- ergonomic
- abbreviatable

Examples:

add (+, a)

delete (-, rm)

done (ok)

move (mv)

note

clear

archive

share

Commands should have a formal grammar.

Example:

verb object modifiers

Natural language parsing and AI interpretation are future features and out of scope.

---

# REST API

Design a clean REST API.

Prefer:

GET
POST
PATCH
DELETE

Do not expose only command endpoints.

Bulk operations are allowed where useful.

The API should be simple enough for:

- CLI clients
- scripts
- messaging interfaces

---

# Output Required

Produce:

1. Domain model explanation
2. PocketBase collection schema
3. Relations
4. Field definitions
5. Validation rules
6. REST API design
7. Example requests/responses
8. Implementation notes

Keep the answer practical.

Avoid unnecessary theory.

If two designs are possible, explain the tradeoff briefly and choose one.

Remember:

The goal is not the most powerful system.

The goal is the smallest, clearest backend that can grow naturally.
