<template>
  <Table class="min-w-full">
    <TableHeader class="bg-gray-50 sticky top-0 z-10">
      <TableRow class="border-b border-gray-200">
        <!-- Checkbox Column -->
        <TableHead class="w-8 px-2 border-r border-gray-200 bg-gray-50">
          <Checkbox 
            :checked="allSelected"
            :indeterminate="someSelected"
            @update:checked="toggleSelectAll"
          />
        </TableHead>
        
        <!-- Dynamic Columns based on visibleFields -->
        <TableHead 
          v-for="field in visibleFieldsData" 
          :key="field.key"
          :class="getColumnClass(field)"
          class="border-r border-gray-200 bg-gray-50 font-medium text-gray-700 text-sm cursor-pointer hover:bg-gray-100"
          @click="handleSort(field.key)"
        >
          <div class="flex items-center space-x-1">
            <span :class="`material-icons text-gray-500 text-sm`">{{ field.icon }}</span>
            <span>{{ field.label }}</span>
            <span v-if="currentSort === field.key" class="material-icons text-gray-400 text-sm">
              {{ sortOrder === 'asc' ? 'north' : 'south' }}
            </span>
          </div>
        </TableHead>
        
        <!-- Add Column Button -->
        <TableHead class="w-8 px-2 bg-gray-50">
          <Button 
            variant="ghost" 
            size="icon" 
            class="h-6 w-6 hover:bg-gray-200"
            @click="$emit('add-field')"
          >
            <span class="material-icons text-gray-400 text-sm">add</span>
          </Button>
        </TableHead>
      </TableRow>
    </TableHeader>
    
    <TableBody>
      <!-- Data Rows -->
      <TableRow 
        v-for="record in records" 
        :key="record.id" 
        :class="getRowClass(record)"
        class="hover:bg-gray-50 border-b border-gray-100 group"
      >
        <!-- Checkbox -->
        <TableCell class="px-2 border-r border-gray-200">
          <Checkbox 
            :checked="selectedRecords.has(record.id)"
            @update:checked="toggleRecordSelection(record.id, $event)"
          />
        </TableCell>
        
        <!-- Dynamic Cells -->
        <TableCell 
          v-for="field in visibleFieldsData" 
          :key="field.key"
          :class="getCellClass(field)"
          class="border-r border-gray-200"
        >
          <RecordCell 
            :record="record"
            :field="field"
            :editable="editRecordsInline"
            :search-query="searchQuery"
            @update="updateRecord(record.id, $event)"
          />
        </TableCell>
        
        <!-- Row Actions -->
        <TableCell class="px-2">
          <div class="flex items-center space-x-1 opacity-0 group-hover:opacity-100">
            <Button 
              v-if="addDeleteRecordsInline"
              variant="ghost" 
              size="icon" 
              class="h-6 w-6 hover:bg-red-100 text-red-600"
              @click="deleteRecord(record.id)"
            >
              <span class="material-icons text-sm">delete</span>
            </Button>
            <Button 
              variant="ghost" 
              size="icon" 
              class="h-6 w-6 hover:bg-gray-200"
              @click="openRecordDetails(record)"
            >
              <span class="material-icons text-gray-400 text-sm">more_horiz</span>
            </Button>
          </div>
        </TableCell>
      </TableRow>
      
      <!-- Add New Row -->
      <TableRow 
        v-if="addDeleteRecordsInline" 
        class="hover:bg-gray-50 border-b border-gray-100 cursor-pointer"
        @click="addRecord"
      >
        <TableCell class="px-2 border-r border-gray-200 text-center">
          <span class="material-icons text-gray-400 text-lg hover:text-gray-600">add</span>
        </TableCell>
        <TableCell 
          v-for="field in visibleFieldsData" 
          :key="field.key"
          class="border-r border-gray-200 text-gray-400 text-sm px-3"
        >
          {{ field.key === 'name' ? 'Add new record...' : '' }}
        </TableCell>
        <TableCell class="px-2"></TableCell>
      </TableRow>
    </TableBody>
  </Table>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from './ui/table'
import { Checkbox } from './ui/checkbox'
import { Button } from './ui/button'
import RecordCell from './RecordCell.vue'

interface Record {
  id: number
  name: string
  assignee?: {
    name: string
    avatar?: string
  }
  status?: string
  attachments?: boolean
  user?: string
  createdAt: Date
  updatedAt: Date
}

interface Field {
  key: string
  label: string
  type: string
  icon: string
}

const props = defineProps<{
  records: Record[]
  visibleFields: string[]
  rowHeight: 'compact' | 'medium' | 'tall' | 'extra-tall'
  searchQuery?: string
  editRecordsInline?: boolean
  addDeleteRecordsInline?: boolean
  colorBy?: string
  currentSort?: string
  sortOrder?: 'asc' | 'desc'
}>()

const emit = defineEmits<{
  'update-record': [id: number, data: Partial<Record>]
  'delete-record': [id: number]
  'add-record': []
  'add-field': []
  'sort': [field: string, order: 'asc' | 'desc']
}>()

// Field definitions
const allFieldsMap = {
  name: { key: 'name', label: 'Name', type: 'text', icon: 'text_fields' },
  assignee: { key: 'assignee', label: 'Assignee', type: 'user', icon: 'person' },
  status: { key: 'status', label: 'Status', type: 'select', icon: 'radio_button_checked' },
  attachmentSummary: { key: 'attachmentSummary', label: 'Attachment Summary', type: 'formula', icon: 'summarize' },
  attachments: { key: 'attachments', label: 'Attachments', type: 'attachment', icon: 'attachment' },
  user: { key: 'user', label: 'User', type: 'text', icon: 'person' },
}

// Selection state
const selectedRecords = ref(new Set<number>())

// Computed properties
const visibleFieldsData = computed(() => 
  props.visibleFields.map(key => allFieldsMap[key as keyof typeof allFieldsMap]).filter(Boolean)
)

const allSelected = computed(() => 
  props.records.length > 0 && selectedRecords.value.size === props.records.length
)

const someSelected = computed(() => 
  selectedRecords.value.size > 0 && selectedRecords.value.size < props.records.length  
)

// Methods
function getColumnClass(field: Field) {
  const baseClass = 'px-3'
  
  switch (field.key) {
    case 'name':
      return `${baseClass} w-48`
    case 'assignee':
      return `${baseClass} w-40`
    case 'status':
      return `${baseClass} w-32`
    case 'attachmentSummary':
      return `${baseClass} w-48`
    case 'attachments':
      return `${baseClass} w-32`
    case 'user':
      return `${baseClass} w-32`
    default:
      return `${baseClass} w-32`
  }
}

function getCellClass(_field: Field) {
  const baseClass = 'px-3'
  const heightClass = getRowHeightClass()
  
  return `${baseClass} ${heightClass}`
}

function getRowClass(record: Record) {
  const baseClass = ''
  const heightClass = getRowHeightClass()
  const colorClass = getRowColorClass(record)
  
  return `${baseClass} ${heightClass} ${colorClass}`
}

function getRowHeightClass() {
  switch (props.rowHeight) {
    case 'compact':
      return 'h-8'
    case 'medium':
      return 'h-10'
    case 'tall':
      return 'h-12'
    case 'extra-tall':
      return 'h-16'
    default:
      return 'h-8'
  }
}

function getRowColorClass(record: Record) {
  if (props.colorBy === 'status' && record.status) {
    switch (record.status) {
      case 'Todo':
        return 'bg-red-50'
      case 'In Progress':
        return 'bg-yellow-50'
      case 'Done':
        return 'bg-green-50'
      default:
        return ''
    }
  }
  return ''
}

function handleSort(fieldKey: string) {
  const newOrder = props.currentSort === fieldKey && props.sortOrder === 'asc' ? 'desc' : 'asc'
  emit('sort', fieldKey, newOrder)
}

function toggleSelectAll(checked: boolean) {
  if (checked) {
    props.records.forEach(record => selectedRecords.value.add(record.id))
  } else {
    selectedRecords.value.clear()
  }
}

function toggleRecordSelection(recordId: number, checked: boolean) {
  if (checked) {
    selectedRecords.value.add(recordId)
  } else {
    selectedRecords.value.delete(recordId)
  }
}

function updateRecord(recordId: number, data: Partial<Record>) {
  emit('update-record', recordId, data)
}

function deleteRecord(recordId: number) {
  selectedRecords.value.delete(recordId)
  emit('delete-record', recordId)
}

function addRecord() {
  emit('add-record')
}

function openRecordDetails(record: Record) {
  // Implement record details modal
  console.log('Open record details for:', record)
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 