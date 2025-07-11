<script lang="ts" setup>
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
  DialogFooter,
} from './ui/dialog'
import { Button } from './ui/button'
import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from './ui/select'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from './ui/table'

const rows = [
  { name: '', notes: '', assignee: '', status: '' },
  { name: '', notes: '', assignee: '', status: '' },
  { name: '', notes: '', assignee: '', status: '' },
]

defineProps<{ open: boolean }>()
defineEmits(['update:open', 'back'])
</script>

<template>
  <Dialog :open="open" @update:open="$emit('update:open', $event)">
    <DialogContent class="max-w-4xl p-0 flex flex-col gap-0">
      <DialogHeader class="p-6 border-b">
        <DialogTitle class="text-2xl font-bold text-gray-900">Connect to a table</DialogTitle>
        <DialogDescription class="text-base text-gray-600">Connect your layout to a table.</DialogDescription>
      </DialogHeader>

      <div class="flex-1 p-8 bg-gray-50">
        <div class="w-1/2 mb-6">
          <label for="table-select" class="block text-sm font-medium text-gray-700 mb-1">Table</label>
          <Select default-value="table1">
            <SelectTrigger id="table-select" class="w-full">
              <SelectValue placeholder="Select a table" />
            </SelectTrigger>
            <SelectContent>
              <SelectGroup>
                <SelectItem value="table1">Table 1</SelectItem>
                <SelectItem value="table2">Table 2</SelectItem>
              </SelectGroup>
            </SelectContent>
          </Select>
        </div>

        <div class="bg-white rounded-lg border border-gray-200 shadow-sm p-4">
          <div class="flex items-center justify-between mb-2">
            <div class="font-semibold text-gray-800">Interface › Table 1</div>
            <div class="flex items-center space-x-2 text-sm text-gray-600">
              <span>Group</span>
              <span>Filter</span>
              <span>Sort</span>
              <span class="material-icons-outlined text-lg">search</span>
            </div>
          </div>
          <Table>
            <TableHeader>
              <TableRow class="border-b-gray-200">
                <TableHead class="font-medium text-gray-500">Name</TableHead>
                <TableHead class="font-medium text-gray-500">Notes</TableHead>
                <TableHead class="font-medium text-gray-500">Assignee</TableHead>
                <TableHead class="font-medium text-gray-500">Status</TableHead>
              </TableRow>
            </TableHeader>
            <TableBody>
              <TableRow v-for="(_, index) in rows" :key="index" class="border-b border-gray-100 last:border-b-0">
                <TableCell class="py-2 text-gray-500">Blank...</TableCell>
                <TableCell class="py-2 text-gray-500">-</TableCell>
                <TableCell class="py-2 text-gray-500">-</TableCell>
                <TableCell class="py-2 text-gray-500">-</TableCell>
              </TableRow>
            </TableBody>
          </Table>
        </div>
      </div>
      
      <DialogFooter class="p-4 border-t bg-white flex justify-between">
        <Button variant="ghost" @click="$emit('back')">Back</Button>
        <Button class="bg-gray-900 text-white hover:bg-black">Finish</Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template> 