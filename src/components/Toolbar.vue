<script lang="ts" setup>
import { ref } from 'vue'
import { Button } from './ui/button'
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from './ui/select'
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from './ui/popover'
import { Input } from './ui/input'
import { Checkbox } from './ui/checkbox'

const emit = defineEmits<{
  'update-search': [value: string]
  'add-record': []
  'apply-filter': [filters: any]
  'apply-sort': [field: string, order: 'asc' | 'desc']
  'apply-group': [field: string]
  'toggle-drawer': []
}>()

// State
const searchQuery = ref('')
const showFilterPopover = ref(false)
const showSortPopover = ref(false)
const showGroupPopover = ref(false)

// Filter state
const statusFilter = ref<string[]>([])
const assigneeFilter = ref<string[]>([])

// Sort state  
const sortField = ref('name')
const sortOrder = ref<'asc' | 'desc'>('asc')

// Group state
const groupField = ref('none')

// Available options
const statusOptions = ['Todo', 'In Progress', 'Done']
const assigneeOptions = ['Shoaib Rehman', 'John Doe', 'Jane Smith']
const sortOptions = [
  { value: 'name', label: 'Name' },
  { value: 'status', label: 'Status' },
  { value: 'assignee', label: 'Assignee' },
  { value: 'user', label: 'User' },
  { value: 'createdAt', label: 'Created' },
  { value: 'updatedAt', label: 'Modified' }
]

// Methods
function handleSearch(event: Event) {
  const target = event.target as HTMLInputElement
  searchQuery.value = target.value
  emit('update-search', target.value)
}

function applyFilters() {
  const filters = {
    status: statusFilter.value,
    assignee: assigneeFilter.value
  }
  emit('apply-filter', filters)
  showFilterPopover.value = false
}

function clearFilters() {
  statusFilter.value = []
  assigneeFilter.value = []
  applyFilters()
}

function applySort() {
  emit('apply-sort', sortField.value, sortOrder.value)
  showSortPopover.value = false
}

function applyGroup() {
  emit('apply-group', groupField.value)
  showGroupPopover.value = false
}

function toggleStatusFilter(status: string, checked: boolean) {
  if (checked) {
    if (!statusFilter.value.includes(status)) {
      statusFilter.value.push(status)
    }
  } else {
    statusFilter.value = statusFilter.value.filter(s => s !== status)
  }
}

function toggleAssigneeFilter(assignee: string, checked: boolean) {
  if (checked) {
    if (!assigneeFilter.value.includes(assignee)) {
      assigneeFilter.value.push(assignee)
    }
  } else {
    assigneeFilter.value = assigneeFilter.value.filter(a => a !== assignee)
  }
}

function addRecord() {
  emit('add-record')
}

function toggleDrawer() {
  emit('toggle-drawer')
}
</script>

<template>
  <div class="flex items-center justify-between h-14 border-b border-gray-200 px-6 bg-white">
    <!-- Left: Table Title -->
    <div class="flex items-center space-x-4">
      <h1 class="text-xl font-semibold text-gray-900">Table 1</h1>
    </div>
    
    <!-- Right: Controls -->
    <div class="flex items-center space-x-3">
      <div class="flex items-center space-x-2">
        <!-- Group Button -->
        <Popover v-model:open="showGroupPopover">
          <PopoverTrigger as-child>
            <Button variant="ghost" class="h-8 px-3 space-x-1 hover:bg-gray-100 text-sm text-gray-700">
              <span class="material-icons text-base">group_work</span>
              <span>Group</span>
              <span v-if="groupField !== 'none'" class="bg-blue-100 text-blue-800 text-xs px-1.5 py-0.5 rounded font-medium">1</span>
            </Button>
          </PopoverTrigger>
          <PopoverContent class="w-64 p-4">
            <div class="space-y-3">
              <h3 class="font-medium text-sm">Group by</h3>
              <Select v-model="groupField">
                <SelectTrigger class="h-8">
                  <SelectValue />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="none">None</SelectItem>
                  <SelectItem value="status">Status</SelectItem>
                  <SelectItem value="assignee">Assignee</SelectItem>
                </SelectContent>
              </Select>
              <div class="flex space-x-2">
                <Button size="sm" @click="applyGroup">Apply</Button>
                <Button size="sm" variant="outline" @click="groupField = 'none'; applyGroup()">Clear</Button>
              </div>
            </div>
          </PopoverContent>
        </Popover>

        <!-- Filter Button -->
        <Popover v-model:open="showFilterPopover">
          <PopoverTrigger as-child>
            <Button variant="ghost" class="h-8 px-3 space-x-1 hover:bg-gray-100 text-sm text-gray-700">
              <span class="material-icons text-base">filter_list</span>
              <span>Filter</span>
              <span v-if="statusFilter.length > 0 || assigneeFilter.length > 0" class="bg-blue-100 text-blue-800 text-xs px-1.5 py-0.5 rounded font-medium">
                {{ statusFilter.length + assigneeFilter.length }}
              </span>
      </Button>
          </PopoverTrigger>
          <PopoverContent class="w-80 p-4">
            <div class="space-y-4">
              <div class="flex items-center justify-between">
                <h3 class="font-medium text-sm">Filters</h3>
                <Button size="sm" variant="ghost" @click="clearFilters">Clear all</Button>
              </div>
              
              <!-- Status Filter -->
              <div>
                <label class="text-sm font-medium text-gray-700 block mb-2">Status</label>
                <div class="space-y-2">
                  <div v-for="status in statusOptions" :key="status" class="flex items-center space-x-2">
                    <Checkbox 
                      :checked="statusFilter.includes(status)"
                      @update:checked="toggleStatusFilter(status, $event)"
                    />
                    <span class="text-sm">{{ status }}</span>
                  </div>
                </div>
              </div>

              <!-- Assignee Filter -->
              <div>
                <label class="text-sm font-medium text-gray-700 block mb-2">Assignee</label>
                <div class="space-y-2">
                  <div v-for="assignee in assigneeOptions" :key="assignee" class="flex items-center space-x-2">
                    <Checkbox 
                      :checked="assigneeFilter.includes(assignee)"
                      @update:checked="toggleAssigneeFilter(assignee, $event)"
                    />
                    <span class="text-sm">{{ assignee }}</span>
                  </div>
                </div>
              </div>

              <div class="flex space-x-2">
                <Button size="sm" @click="applyFilters">Apply</Button>
                <Button size="sm" variant="outline" @click="showFilterPopover = false">Cancel</Button>
              </div>
            </div>
          </PopoverContent>
        </Popover>

        <!-- Sort Button -->
        <Popover v-model:open="showSortPopover">
          <PopoverTrigger as-child>
            <Button variant="ghost" class="h-8 px-3 space-x-1 hover:bg-gray-100 text-sm text-gray-700">
              <span class="material-icons text-base">sort</span>
              <span>Sort</span>
              <span class="bg-gray-900 text-white text-xs px-1.5 py-0.5 rounded font-medium">1</span>
        </Button>
          </PopoverTrigger>
          <PopoverContent class="w-64 p-4">
            <div class="space-y-3">
              <h3 class="font-medium text-sm">Sort by</h3>
              <Select v-model="sortField">
                <SelectTrigger class="h-8">
                  <SelectValue />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem v-for="option in sortOptions" :key="option.value" :value="option.value">
                    {{ option.label }}
                  </SelectItem>
                </SelectContent>
              </Select>
              
              <div class="space-y-2">
                <label class="text-sm font-medium text-gray-700">Order</label>
                <div class="flex space-x-2">
                  <Button 
                    size="sm" 
                    :variant="sortOrder === 'asc' ? 'default' : 'outline'"
                    @click="sortOrder = 'asc'"
                  >
                    <span class="material-icons text-sm mr-1">north</span>
                    Ascending
        </Button>
                  <Button 
                    size="sm" 
                    :variant="sortOrder === 'desc' ? 'default' : 'outline'"
                    @click="sortOrder = 'desc'"
                  >
                    <span class="material-icons text-sm mr-1">south</span>
                    Descending
        </Button>
                </div>
              </div>

              <div class="flex space-x-2">
                <Button size="sm" @click="applySort">Apply</Button>
                <Button size="sm" variant="outline" @click="showSortPopover = false">Cancel</Button>
              </div>
            </div>
          </PopoverContent>
        </Popover>
      </div>
      
      <div class="flex items-center space-x-2">
        <!-- Search Input -->
        <div class="relative">
          <span class="material-icons absolute left-2 top-1/2 transform -translate-y-1/2 text-gray-400 text-base">search</span>
          <Input 
            v-model="searchQuery"
            placeholder="Search records..."
            class="h-8 pl-8 pr-3 w-48 text-sm border-gray-200 focus:ring-1 focus:ring-blue-500 focus:border-blue-500"
            @input="handleSearch"
          />
        </div>
        
        <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100" @click="toggleDrawer">
          <span class="material-icons text-base text-gray-600">settings</span>
        </Button>
        
        <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100">
          <span class="material-icons text-base text-gray-600">more_vert</span>
        </Button>
        
        <Button class="h-8 px-4 bg-blue-600 hover:bg-blue-700 text-white text-sm font-medium" @click="addRecord">
          Add record
        </Button>
      </div>
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 