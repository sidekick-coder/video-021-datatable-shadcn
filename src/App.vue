<script setup lang="ts">
import DataTable, { defineColumns } from '@/components/DataTable.vue'
import { faker } from '@faker-js/faker'
import Card from './components/ui/card/Card.vue';
import { ref } from 'vue';

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
    field: row => row.tags.join()
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
      <DataTable :columns="columns" :rows="rows" />
    </Card>
  </div>
</template>
