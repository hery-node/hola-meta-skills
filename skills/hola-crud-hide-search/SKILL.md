---
name: hola-crud-hide-search
description: Hide specific fields from the search form while keeping server-side filtering. Use when entity fields need search:true for server-side filtering (via filter prop) but should not appear in the user-facing search form UI.
---

# CRUD Hide Search Fields

Hide specific fields from the search form while keeping server-side filtering enabled.

## When to Use This Skill

- Server field has `search: true` for **filtering via `:filter` prop**
- But field should **NOT appear in the user-facing search form**
- Common case: **project-scoped views** where project is always filtered but users don't need a project dropdown

## Quick Answer

```vue
<h-crud entity="prd" :filter="{ project: projectId }" :hide-search="['project']" ... />
```

## The Problem

When you use `:filter` prop to permanently filter by a field:

```vue
<h-crud entity="prd" :filter="{ project: currentProject._id }" ... />
```

The server needs `search: true` on the field to accept filters. But this also makes the field appear in the search form UI -- which is confusing when the field is already pre-filtered.

## Solution: hideSearch Prop

Use `hideSearch` to hide fields from search form while keeping server filtering:

### Step 1: Enable Search on Server (Required for Filtering)

```typescript
// server/router/prd.ts
{ name: "project", type: "string", ref: "project", search: true, list: false }
```

### Step 2: Hide from Search Form on Client

```vue
<h-crud entity="prd" :filter="{ project: currentProject._id }" :hide-search="['project']" :sort-key="['created_at']" :sort-desc="[true]" />
```

## Common Patterns

### Pattern 1: Project-Scoped Views

```vue
<!-- In project views, filter by project but hide from search -->
<h-crud entity="task" :filter="{ project: currentProject._id }" :hide-search="['project']" item-label-key="title" />
```

### Pattern 2: Tenant/Organization Scoping

```vue
<!-- Multi-tenant apps: filter by tenant, hide from search -->
<h-crud entity="document" :filter="{ tenant: currentTenant._id }" :hide-search="['tenant']" />
```

### Pattern 3: User-Scoped Data

```vue
<!-- Filter by current user, hide from search -->
<h-crud entity="my_files" :filter="{ owner: currentUser._id }" :hide-search="['owner']" />
```

### Pattern 4: Multiple Hidden Fields

```vue
<h-crud entity="invoice" :filter="{ organization: orgId, year: currentYear }" :hide-search="['organization', 'year']" />
```

## How It Works

```
┌──────────────────────────────────────────────────────────────┐
│ Server: search: true                                         │
│   ↓                                                          │
│ Field can be used in query filters (via :filter prop)        │
│   ↓                                                          │
│ Field appears in search form (default behavior)              │
│   ↓                                                          │
│ Client: :hide-search="['field']"                             │
│   ↓                                                          │
│ Field hidden from search form, but filter still works        │
└──────────────────────────────────────────────────────────────┘
```

## Comparison with hideColumns

| Prop          | Affects       | Use Case                      |
| ------------- | ------------- | ----------------------------- |
| `hideColumns` | Table columns | Hide fields from list display |
| `hideSearch`  | Search form   | Hide fields from search form  |
| `filter`      | Query results | Pre-filter data server-side   |

They work together:

```vue
<h-crud entity="task" :filter="{ project: projectId }" :hide-search="['project']" :hide-columns="['project']" />
```

## Important Notes

1. **Server `search: true` is required** - If the field has `search: false` on server, filtering won't work at all
2. **hideSearch is client-side only** - It only hides the UI, the field can still be filtered
3. **Use with filter prop** - hideSearch is most useful when combined with `:filter` for permanent filtering

## Troubleshooting

### Filter not working even with search: true

**Solution:** Restart the server. Entity metadata is cached.

```bash
cd source/server
bun run dev
```

### Field still in search form after adding hideSearch

**Solution:** Check the prop name is correct - it's `hide-search` (kebab-case in template) or `hideSearch` (camelCase in props).

## Related Skills

- **hola-crud-table-search** - Control `search` property on server
- **hola-crud-table-list** - Control `list` property and hideColumns
