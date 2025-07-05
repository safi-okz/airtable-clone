<template>
  <div class="flex flex-col h-full bg-gray-100">
    <!-- Breadcrumb -->
    <div class="flex items-center px-6 py-2 bg-white border-b border-gray-200">
      <span class="font-semibold text-lg text-gray-700">Student record</span>
      <span class="mx-2 text-gray-400 font-light text-2xl">&gt;</span>
      <span class="font-semibold text-lg text-blue-600">Table 1</span>
    </div>
    
    <!-- Controls -->
    <div class="flex items-center px-4 py-2 bg-white border-b border-gray-200 gap-2 text-sm">
      <Button variant="ghost" class="h-8 px-2 space-x-1 text-gray-700 font-medium">
        <span class="material-icons-outlined text-base">group</span><span>Group</span>
      </Button>
      <Button variant="ghost" class="h-8 px-2 space-x-1 text-gray-700 font-medium">
        <span class="material-icons-outlined text-base">filter_list</span><span>Filter</span>
      </Button>
      <Button variant="ghost" class="h-8 px-2 space-x-1 text-gray-700 font-medium">
        <span class="material-icons-outlined text-base">sort</span><span>Sort</span>
      </Button>
      <div class="flex-1 flex justify-end items-center space-x-2">
         <Button variant="ghost" size="icon" class="h-8 w-8">
            <span class="material-icons-outlined text-gray-500">search</span>
         </Button>
         <Button variant="ghost" size="icon" class="h-8 w-8">
            <span class="material-icons-outlined text-gray-500">more_horiz</span>
         </Button>
      </div>
    </div>

    <!-- Table -->
    <div class="flex-1 overflow-auto p-4">
      <div class="bg-white rounded border border-gray-200 shadow-sm">
        <Table>
          <TableHeader>
            <TableRow class="border-b-gray-200">
              <TableHead class="px-4 py-2 font-medium text-gray-600">ID</TableHead>
              <TableHead class="px-4 py-2 font-medium text-gray-600">Name</TableHead>
              <TableHead class="px-4 py-2 font-medium text-gray-600">F/ Name</TableHead>
              <TableHead class="px-4 py-2 font-medium text-gray-600">Class</TableHead>
              <TableHead class="px-4 py-2 font-medium text-gray-600">Phone Num</TableHead>
            </TableRow>
          </TableHeader>
          <TableBody>
            <TableRow v-for="row in rows" :key="row.id" class="border-b border-gray-200 last:border-b-0">
              <TableCell class="px-4 py-2 text-gray-500">{{ row.id }}</TableCell>
              <TableCell class="px-4 py-2 text-gray-500">Blank...</TableCell>
              <TableCell class="px-4 py-2 text-gray-500">-</TableCell>
              <TableCell class="px-4 py-2 text-gray-500">-</TableCell>
              <TableCell class="px-4 py-2 text-gray-500">-</TableCell>
            </TableRow>
          </TableBody>
        </Table>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { Button } from './ui/button'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from './ui/table'

const rows = [
  { id: 1, name: '', fname: '', class: '', phone: '' },
  { id: 2, name: '', fname: '', class: '', phone: '' },
  { id: 3, name: '', fname: '', class: '', phone: '' },
]
</script> 