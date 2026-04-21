<script setup lang="ts">
import { computed } from 'vue'
import SelectTrigger from './ui/select/SelectTrigger.vue'
import SelectValue from './ui/select/SelectValue.vue'
import SelectContent from './ui/select/SelectContent.vue'
import SelectGroup from './ui/select/SelectGroup.vue'
import SelectItem from './ui/select/SelectItem.vue'
import Button from './ui/button/Button.vue'
import { ChevronLeft, ChevronRight, ChevronsLeft, ChevronsRight } from 'lucide-vue-next'
import Select from './ui/select/Select.vue'

const page = defineModel('page', {
    type: Number,
    required: true,
})

const totalRows = defineModel('totalRows', {
    type: Number,
    required: true,
})

const totalPages = defineModel('totalPages', {
    type: Number,
    required: true,
})


const limit = defineModel('limit', {
    type: Number,
    required: true,
})

const limitOptions = defineModel('limitOptions', {
    type: Array as () => number[],
    required: false,
    default: () => [10, 20, 30, 40, 50, 100],
})

const visiblePages = computed(() => {
    const current = page.value
    const total = totalPages.value
    const pages: number[] = []

    if (total <= 3) {
        // Show all pages if total is 3 or less
        for (let i = 1; i <= total; i++) {
            pages.push(i)
        }
        return pages
    }

    // Calculate the range of 3 pages to show
    let start = Math.max(1, current - 1)
    const end = Math.min(total, start + 2)

    // Adjust start if end is at the boundary
    if (end - start < 2) {
        start = Math.max(1, end - 2)
    }

    for (let i = start; i <= end; i++) {
        pages.push(i)
    }

    return pages
})
</script>

<template>
    <div class="flex flex-col sm:flex-row items-center justify-between px-2 gap-4">
        <div class="flex-1 order-2 sm:order-1">
            <slot name="caption" />
        </div>

        <div class="flex flex-col sm:flex-row items-center space-y-4 sm:space-y-0 sm:space-x-2 order-1 sm:order-2">
            <Select v-model.number="limit">
                <SelectTrigger class="w-[50px]">
                    {{ limit }}
                </SelectTrigger>
                <SelectContent>
                    <SelectGroup>
                        <SelectItem v-for="o in limitOptions" :value="o">
                            {{  o }}
                        </SelectItem>
                    </SelectGroup>
                </SelectContent>
            </Select>

            <div class="flex  space-x-2">
                <Button 
                    v-for="p in visiblePages" 
                    :key="p"
                    :variant="page === p ? 'default' : 'outline'" 
                    class="w-8 h-8 p-0 sm:hidden"
                    @click="page = p"
                >
                    {{ p }}
                </Button>
            </div>


            <div class="flex items-center  space-x-2">
                <Button variant="outline" class="w-8 h-8 p-0" :disabled="page === 1" @click="page = 1">
                    <ChevronsLeft class="w-4 h-4" />
                </Button>
                <Button variant="outline" class="w-8 h-8 p-0" :disabled="page === 1" @click="page = page - 1">
                    <ChevronLeft class="w-3 h-3 sm:w-4 sm:h-4" />
                </Button>
                
                <Button 
                    v-for="p in visiblePages" 
                    :key="p"
                    :variant="page === p ? 'default' : 'outline'" class="w-8 h-8 p-0 hidden sm:flex"
                    @click="page = p"
                >
                    {{ p }}
                </Button>
                
                <Button
                    variant="outline" 
                    class="w-8 h-8 p-0" 
                    :disabled="page === totalPages || totalPages === 0"
                    @click="page = page + 1"
                >
                    <ChevronRight class="w-3 h-3 sm:w-4 sm:h-4" />
                </Button>

                <Button 
                    variant="outline" 
                    class="w-8 h-8 p-0" 
                    :disabled="page === totalPages || totalPages === 0"
                    @click="page = totalPages"
                >
                    <ChevronsRight class="w-4 h-4" />
                </Button>
            </div>
        </div>
    </div>
</template>