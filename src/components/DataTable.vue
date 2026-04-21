<script lang="ts">
export interface DataTableColumn<T extends Record<string, any> = Record<string, any>> {
    id?: string
    label?: string
    field?: keyof T | ((row: T) => any) | (string & {})
}

export interface DataTableSort<T extends Record<string, any> = Record<string, any>> {
    key: keyof T | (string & {})
    direction?: 'asc' | 'desc'
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
import Checkbox from './ui/checkbox/Checkbox.vue'

const props = defineProps({
    itemKey: {
        type: String as () => keyof T | (string & {}),
        default: null
    },
    enableSelection: {
        type: Boolean,
        default: false
    }
})

const selected = defineModel('selected', {
    type: Array as () => (string | number)[],
    default: () => []
})

const rows = defineModel('rows', {
    type: Array as () => T[],
    default: () => []
})

const columns = defineModel('columns', {
    type: Array as () => DataTableColumn<T>[],
    default: () => []
})

const sort = defineModel('sort', {
    type: Array as () => DataTableSort<T>[],
    default: () => []
})

function findFieldValue(row: T, column: DataTableColumn<T>) {
    if (typeof column.field === 'function') {
        return column.field(row)
    }

    return get(row, column.field as string)
}

function isSelected(row: T) {
    if (!props.itemKey) return

    const key = get(row, props.itemKey as string)

    if (!key) return

    return selected.value.includes(key)
}

function toggle(row: T) {
    if (!props.itemKey) return

    const key = get(row, props.itemKey as string)

    if (!key) return

    if (!selected.value.includes(key)) {
        selected.value.push(key)
        return
    }

    const index = selected.value.indexOf(key)

    selected.value.splice(index, 1)
}

</script>

<template>
    <Table>
        <TableHeader>
            <TableRow>
                <TableHead v-if="enableSelection" class="w-[50px]" />

                <TableHead v-for="(c, index) of columns" :key="index">
                    <slot :name="`column-${c.id}`" :column="c">
                        {{ c.label }}
                    </slot>
                </TableHead>
            </TableRow>
        </TableHeader>
        <TableBody>
            <TableRow v-for="(r, ri) of rows" :key="ri">
                <TableCell v-if="enableSelection" class="w-[50px]">
                    <Checkbox :model-value="isSelected(r)" @update:model-value="toggle(r)" />
                </TableCell>
                <TableCell v-for="(c, ci) of columns" :key="ci">
                    <slot :name="`row-${c.id}`" :row="r" :column="c">
                        {{ findFieldValue(r, c) }}
                    </slot>
                </TableCell>
            </TableRow>
        </TableBody>
    </Table>
</template>
