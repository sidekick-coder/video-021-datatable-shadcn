# Shadcn ui DataTable component 

```vue 
<script setup lang="ts">
import DataTable, { defineColumns } from '@/components/DataTable'

type User = {
  id: number
  name: string
  email: string
}

const columns = defineColumns<User>([
  { id: 'id', label: 'ID', field: 'id', width: 80, sortable: true },
  { id: 'name', label: 'Nome', field: 'name', sortable: true },
  { id: 'email', label: 'Email', field: 'email' }
])

const rows: User[] = [
  { id: 1, name: 'Rick', email: 'rick@email.com' },
  { id: 2, name: 'Morty', email: 'morty@email.com' }
]
</script>

<template>
  <DataTable :columns="columns" :rows="rows">
    <!-- custom header -->
    <template #header-name="{ column }">
      <span>👤 {{ column.label }}</span>
    </template>

    <!-- custom cell -->
    <template #row-email="{ row }">
      <a :href="`mailto:${row.email}`">{{ row.email }}</a>
    </template>
  </DataTable>
</template>
```
