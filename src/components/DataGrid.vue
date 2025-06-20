<template>
  <div class="flex-1 bg-white overflow-auto">
    <div class="border border-gray-200 rounded-lg shadow-sm">
      <Table>
        <TableHeader class="bg-gray-50 sticky top-0">
          <TableRow class="border-b border-gray-200">
            <TableHead class="w-12 px-3 border-r border-gray-200 bg-gray-50">
              <Checkbox class="data-[state=checked]:bg-blue-600" />
            </TableHead>
            <TableHead class="w-12 px-3 border-r border-gray-200 bg-gray-50" />
            <TableHead v-for="header in headers" :key="header.key" class="border-r border-gray-200 px-3 h-10 font-semibold text-gray-700 bg-gray-50">
              {{ header.label }}
            </TableHead>
          </TableRow>
        </TableHeader>
        <TableBody>
          <TableRow v-for="student in students" :key="student.id" class="h-10 hover:bg-gray-50 transition-colors border-b border-gray-100">
            <TableCell class="border-r border-gray-200 px-3">
              <Checkbox class="data-[state=checked]:bg-blue-600" />
            </TableCell>
            <TableCell class="border-r border-gray-200 px-3 text-center text-gray-500">
              <span class="material-icons-outlined text-base">drag_indicator</span>
            </TableCell>
            <TableCell v-for="header in headers" :key="header.key" class="border-r border-gray-200 px-3 text-sm text-gray-800">
              {{ student[header.key] }}
            </TableCell>
          </TableRow>
          <TableRow class="h-10 hover:bg-gray-50 transition-colors">
             <TableCell class="border-r border-gray-200 px-3 text-center">
               <span class="material-icons text-lg text-gray-400 hover:text-gray-600 cursor-pointer transition-colors">add</span>
              </TableCell>
          </TableRow>
        </TableBody>
      </Table>
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from './ui/table'
import { Checkbox } from './ui/checkbox'

interface Student {
  id: number;
  name: string;
  class: string;
  status: string;
  grade: string;
  attendance: string;
  email: string;
  [key: string]: number | string;
}

const students: Student[] = [
  { id: 1, name: 'John Doe', class: 'A', status: 'Active', grade: '85', attendance: '95%', email: 'john.doe@example.com' },
  { id: 2, name: 'Jane Smith', class: 'B', status: 'Active', grade: '92', attendance: '98%', email: 'jane.smith@example.com' },
  { id: 3, name: 'Mike Johnson', class: 'A', status: 'Inactive', grade: '78', attendance: '80%', email: 'mike.johnson@example.com' },
  { id: 4, name: 'Emily Davis', class: 'C', status: 'Active', grade: '95', attendance: '100%', email: 'emily.davis@example.com' },
  { id: 5, name: 'Chris Lee', class: 'B', status: 'Active', grade: '88', attendance: '92%', email: 'chris.lee@example.com' },
  { id: 6, name: '', class: '', status: '', grade: '', attendance: '', email: '' },
  { id: 7, name: '', class: '', status: '', grade: '', attendance: '', email: '' },
  { id: 8, name: '', class: '', status: '', grade: '', attendance: '', email: '' },
]

const headers = [
  { key: 'name', label: 'Name' },
  { key: 'class', label: 'Class' },
  { key: 'status', label: 'Status' },
  { key: 'grade', label: 'Grade' },
  { key: 'attendance', label: 'Attendance' },
  { key: 'email', label: 'Email' },
]
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 