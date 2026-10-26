# Data Model: RSS Subscription List

## Subscription

Represents one non-empty value submitted by the user and displayed in the list.

| Field | Meaning | Rules |
|---|---|---|
| `url` | The submitted subscription value | Must not be null, empty, or whitespace-only. No URL syntax or reachability validation is performed. Non-empty values are retained as entered. |
| `position` | The value's position in the list | Assigned by insertion order; earlier accepted values remain before later values. |

Duplicate `url` values are distinct list entries. The position is implicit in the
ordered collection and is not exposed as a separate API field.

## Subscription List

The list contains zero or more `Subscription` entries and is the API's current
process state. It starts empty, changes only when a non-empty value is accepted,
and is cleared when the API process stops. It has no persistence or multi-user
partitioning.

## State Transitions

```text
Empty list -- add non-empty value --> One-entry list
Existing list -- add non-empty value --> Existing entries plus new final entry
Any list -- add empty/whitespace-only value --> Unchanged list
Any list -- API process restarts --> Empty list
```

Feed fetching, item state, read status, and persistence are outside this model and
the MVP scope.