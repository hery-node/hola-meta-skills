---
name: hola-crud-click-chip
description: Make reference chips clickable in h-crud tables to navigate to detail pages. Use when implementing clickable chips that navigate to related entity detail views, such as clicking a job chip to navigate to the job detail page.
---

# Clickable Chips in h-crud Tables

This skill enables navigation when clicking on reference field chips (ref fields) in h-crud/DataTable components.

## How It Works

Reference fields in h-crud are automatically rendered as chips. To make them clickable and navigate to a detail page:

1. Add a `click` handler in the headers configuration
2. Enable `chip-clickable` prop on the h-crud component

## Implementation

### Step 1: Add Router Import

```typescript
import { useRouter } from "vue-router";

const router = useRouter();
```

### Step 2: Add Click Handler to Headers

In the headers array, add a `click` function for the reference field:

```typescript
const headers = [
  {
    name: "job", // The ref field name
    click: (id: string, ref: string, label: string) => {
      // id: the _id of the referenced entity
      // ref: the entity type (e.g., 'job')
      // label: the display label
      router.push({ name: "job-detail", params: { id } });
    },
  },
  // ... other headers
];
```

### Step 3: Enable chip-clickable Prop

Add `chip-clickable` to the h-crud component:

```vue
<h-crud entity="task" :headers="headers" chip-clickable ... />
```

## Complete Example

```vue
<script setup lang="ts">
import { useRouter } from "vue-router";

const router = useRouter();

const headers = [
  { name: "title", width: "300px" },
  {
    name: "job",
    click: (id: string) => {
      router.push({ name: "job-detail", params: { id } });
    },
  },
  { name: "status" },
];
</script>

<template>
  <h-crud entity="task" :headers="headers" chip-clickable item-label-key="title" :sort-key="['created_at']" :sort-desc="[true]" />
</template>
```

## Click Handler Signature

The click handler receives three parameters:

| Parameter | Type   | Description                        |
| --------- | ------ | ---------------------------------- |
| id        | string | The `_id` of the referenced entity |
| ref       | string | The entity type (from field.ref)   |
| label     | string | The display label of the chip      |

## Key Points

- The `chip-clickable` prop must be set on h-crud for click handlers to work
- The `click` function is called when the chip is clicked
- Use `@click.stop` internally to prevent row click events from firing
- Works with both single ref fields and array ref fields

## Related Components

- `h-crud` (CrudTable) - The CRUD table wrapper component
- `DataTable` - The underlying data table component that handles chip rendering
