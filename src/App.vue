<script setup lang="ts">
import DataTable, { defineColumns, type DataTableSort } from '@/components/DataTable.vue'
import { faker } from '@faker-js/faker'
import Card from './components/ui/card/Card.vue';
import { ref } from 'vue';
import { Calendar, Tag } from 'lucide-vue-next';
import Badge from './components/ui/badge/Badge.vue';
import CardHeader from './components/ui/card/CardHeader.vue';
import CardTitle from './components/ui/card/CardTitle.vue';

interface Product {
  id: number
  status: 'active' | 'inactive'
  price: number
  tags: string[]
  created_at: Date
  updated_at: Date
}

const columns = defineColumns<Product>([
  {
    id: 'id',
    label: 'id',
    field: 'id'
  },
  {
    id: 'price',
    label: 'Price',
    field: row => `R$ ${row.price}`
  },
  {
    id: 'status',
    label: 'Status',
    field: 'status'
  },
  {
    id: 'tags',
    label: 'Tags',
    field: row => row.tags.join(),
    sortable: false
  },
  {
    id: 'created_at',
    label: 'Created at',
    field: row => row.created_at.toLocaleString()
  },
  {
    id: 'updated_at',
    label: 'Updated at',
    field: row => row.updated_at.toLocaleString()
  },
])

const rows = ref<Product[]>([])
const selected = ref([])
const sort = ref<DataTableSort[]>([{
  key: 'id',
  direction: 'desc'
}])

function generate(length = 20): Product[] {
  const rows: Product[] = []

  for (let i = 0; i < length; i++) {
    rows.push({
      id: i,
      status: faker.helpers.arrayElement(['active', 'inactive']),
      price: Number(faker.commerce.price({ min: 10, max: 1000, dec: 2 })),
      tags: faker.helpers.arrayElements(
        ['electronics', 'clothing', 'home', 'sports', 'books'],
        { min: 1, max: 3 }
      ),
      created_at: faker.date.past(),
      updated_at: faker.date.recent()
    })
  }

  return rows
}

rows.value = generate()
</script>

<template>
  <div class="flex h-screen w-screen items-center justify-center">
    <Card class="w-[1200px]">
      <CardHeader class="border-b">
        <CardTitle>Data Table</CardTitle>

        <div>{{ selected }}</div>
      </CardHeader>

      <DataTable 
        v-model:selected="selected" 
        v-model:sort="sort" 
        :columns="columns" 
        :rows="rows" 
        item-key="id" 
        enable-selection
      >

        <template #column-tags="{ column }">
          <div class="flex items-center gap-2">
            <Tag :size="16"></Tag>
            <span>{{ column.label }}</span>
          </div>
        </template>

        <template #column-created_at="{ column }">
          <div class="flex items-center gap-2">
            <Calendar :size="16" />
            <span>{{ column.label }}</span>
          </div>
        </template>

        <template #column-updated_at="{ column }">
          <div class="flex items-center gap-2">
            <Calendar :size="16" />
            <span>{{ column.label }}</span>
          </div>
        </template>

        <template #row-status="{ row }">
          <div :class="row.status === 'active' ? 'text-green-500' : 'text-red-500'" class="font-bold uppercase text-xs">
            {{ row.status }}
          </div>
        </template>

        <template #row-tags="{ row }">
          <div class="flex gap-1">
            <Badge v-for="t of row.tags" :key="t">
              {{ t }}
            </Badge>
          </div>
        </template>
      </DataTable>
    </Card>
  </div>
</template>
