<script lang="ts">
export interface DataTableColumn<T extends Record<string, any> = Record<string, any>> {
    id?: string
    label?: string
    field?: keyof T | ((row: T) => any) | (string & {})
}

export function defineColumns<T extends Record<string, any>>(columns: DataTableColumn<T>[]) {
    return columns
}
</script>

<script setup lang="ts" generic="T extends Record<string, any> = Record<string, any>">
import {
    Table,
    TableBody,
    TableCell,
    TableHead,
    TableHeader,
    TableRow,
} from '@/components/ui/table'
import { get } from 'lodash-es'

const rows = defineModel('rows', {
    type: Array as () => T[],
    default: () => []
})

const columns = defineModel('columns', {
    type: Array as () => DataTableColumn<T>[],
    default: () => []
})

function findFieldValue(row: T, column: DataTableColumn<T>) {
    if (typeof column.field === 'function') {
        return column.field(row)
    }

    return get(row, column.field as string)
}

</script>

<template>
    <Table>
        <TableHeader>
            <TableRow>
                <TableHead v-for="(c, index) of columns" :key="index">
                    <slot :name="`column-${c.id}`" :column="c">
                        {{ c.label }}
                    </slot>
                </TableHead>
            </TableRow>
        </TableHeader>
        <TableBody>
            <TableRow v-for="(r, ri) of rows" :key="ri">
                <TableCell v-for="(c, ci) of columns" :key="ci">
                    <slot :name="`row-${c.id}`" :row="r" :column="c">
                        {{ findFieldValue(r, c) }}
                    </slot>
                </TableCell>
            </TableRow>
        </TableBody>
    </Table>
</template>
