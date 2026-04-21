<script setup lang="ts">
import DataTable from '@/components/DataTable.vue'
import Card from './components/ui/card/Card.vue';

interface Column {}
interface Row {}

const columns = [] as Column[]
const rows = [] as Row[]

</script>

<template>
  <div class="flex h-screen w-screen items-center justify-center">
    <Card class="w-[500px]">      
      <DataTable :columns="columns" :rows="rows" />
    </Card>
  </div>
</template>
