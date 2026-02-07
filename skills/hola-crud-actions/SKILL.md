---
name: hola-crud-actions
description: Pattern for defining custom action buttons in h-crud component with conditional visibility (shown) and click handlers (handle). Used when you need to add action buttons to entity list views that show/hide based on entity state.
---

# Hola CRUD Actions Skill

## Overview

The `h-crud` component supports custom action buttons via the `:actions` prop. Each action can have conditional visibility using the `shown` property.

## Action Interface

```typescript
interface CrudAction {
  icon: string; // MDI icon name, e.g. 'mdi-play'
  color: string; // Vuetify color, e.g. 'success', 'warning', 'error'
  tooltip: string; // Tooltip text (use i18n: t('key'))
  animate?: boolean; // Optional: animate icon on loading
  shown?: (item: any) => boolean; // Conditional visibility based on item data
  handle: (item: any) => Promise<void> | void; // Click handler
}
```

## Key Points

1. **Property name is `shown`, not `show`** - hola-web uses `v-show` with `shown` callback
2. **Item values are translated** - enum fields are passed as translated strings (e.g., `"Idle"` not `0`)
3. **Use i18n for comparisons** - compare with `t('type.field_value')` not hardcoded strings

## Example: Status-Based Actions

```typescript
const actions = [
  // Show when status is idle
  {
    icon: "mdi-play",
    color: "success",
    tooltip: t("action.start"),
    shown: (item: any) => !item.status || item.status === t("type.status_idle"),
    handle: async (item) => {
      await axiosPost(`/entity/${item.id}/start`, {});
      crudTable.value?.refresh();
    },
  },
  // Show when status is running
  {
    icon: "mdi-stop",
    color: "warning",
    tooltip: t("action.stop"),
    shown: (item: any) => item.status === t("type.status_running"),
    handle: async (item) => {
      await axiosPost(`/entity/${item.id}/stop`, {});
      crudTable.value?.refresh();
    },
  },
];
```

## Template Usage

```vue
<h-crud entity="my_entity" :actions="actions" @row-dblclick="handleRowClick" />
```

## Common Patterns

### Always Visible Action

Omit the `shown` property:

```typescript
{ icon: 'mdi-delete', color: 'error', tooltip: t('common.delete'), handle: deleteItem }
```

### Multiple Conditions

```typescript
shown: (item) => item.status === t("type.status_ready") && item.is_active;
```

### Handle with Confirmation

```typescript
handle: async (item) => {
  const confirmed = await confirmDialog.value?.open(title, message);
  if (confirmed) {
    await axiosPost(`/entity/${item.id}/action`, {});
    crudTable.value?.refresh();
  }
};
```
