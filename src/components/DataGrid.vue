<template>
  <ScrollArea class="flex-1 bg-gray-50">
    <Table class="min-w-full border-separate border-spacing-0">
      <TableHeader>
        <TableRow class="bg-white border-b border-gray-200">
          <TableHead class="w-8 text-center font-normal text-gray-400"></TableHead>
          <TableHead v-for="field in fields" :key="field" class="px-4 py-2 font-semibold text-gray-700 text-left border-r border-gray-100 whitespace-nowrap">
            <div class="flex items-center gap-1">
              <span v-if="field === 'Attachments'" class="material-icons text-base">attach_file</span>
              <span v-else-if="field === 'Phone Number'" class="material-icons text-base">phone</span>
              <span v-else>{{ field }}</span>
              <span class="material-icons text-xs text-gray-400 cursor-pointer">expand_more</span>
            </div>
          </TableHead>
          <TableHead class="w-8 text-center font-normal text-gray-400 cursor-pointer">+</TableHead>
        </TableRow>
      </TableHeader>
      <TableBody>
        <TableRow v-for="row in rows" :key="row.id" class="bg-white border-b border-gray-100 hover:bg-gray-100">
          <TableCell class="text-center text-gray-400">{{ row.id }}</TableCell>
          <TableCell v-for="field in fields" :key="field" class="px-4 py-2 border-r border-gray-100">
            <span v-if="field === 'Attachments'" class="material-icons text-base text-gray-400">attach_file</span>
            <span v-else-if="field === 'Phone Number'" class="text-gray-700">{{ row[field] || '' }}</span>
            <span v-else>{{ row[field] || '' }}</span>
          </TableCell>
          <TableCell class="text-center text-gray-400 cursor-pointer">+</TableCell>
        </TableRow>
        <TableRow class="bg-white">
          <TableCell class="text-center text-gray-400 cursor-pointer">+</TableCell>
          <TableCell v-for="field in fields" :key="field"></TableCell>
          <TableCell></TableCell>
        </TableRow>
      </TableBody>
    </Table>
  </ScrollArea>
</template>

<script lang="ts" setup>
import { ScrollArea } from '@/components/ui/scroll-area'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from '@/components/ui/table'

const fields = [
  'Name',
  'Notes',
  'Assignee',
  'Status',
  'Attachments',
  'Phone Number',
]
const rows: Record<string, any>[] = [
  { id: 1 },
  { id: 2 },
  { id: 3 },
]
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 