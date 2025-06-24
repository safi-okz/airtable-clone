<template>
  <div class="flex-1 bg-white overflow-hidden relative">
    <!-- Main Table Area (Full Width) -->
    <div class="w-full h-full overflow-auto">
      <!-- Search Results -->
      <div v-if="searchQuery && filteredAndSortedRecords.length === 0" class="p-4 text-center text-gray-500">
        No records found for "{{ searchQuery }}"
      </div>
      
      <!-- Grouped View -->
      <div v-if="groupBy !== 'none'" class="p-4 space-y-4">
        <div v-for="group in groupedRecords" :key="group.key" class="border border-gray-200 rounded-lg">
          <div class="bg-gray-50 px-4 py-3 border-b border-gray-200 flex items-center justify-between">
            <div class="flex items-center space-x-2">
              <Button 
                variant="ghost" 
                size="icon" 
                class="h-5 w-5"
                @click="toggleGroupCollapse(group.key)"
              >
                <span class="material-icons text-sm">
                  {{ collapsedGroups.has(group.key) ? 'chevron_right' : 'expand_more' }}
                </span>
              </Button>
              <span class="font-medium">{{ group.key || 'No ' + groupBy }}</span>
              <span class="text-sm text-gray-500">({{ group.records.length }})</span>
            </div>
          </div>
          <div v-if="!collapsedGroups.has(group.key)">
            <SimpleTable 
              :records="group.records" 
              :visible-fields="visibleFields"
              :row-height="rowHeight"
              :color-by="colorBy"
              :edit-records-inline="editRecordsInline"
              :add-delete-records-inline="addDeleteRecordsInline"
              :click-into-record-details="clickIntoRecordDetails"
              :current-sort="sortBy"
              :sort-order="sortOrder"
              @update-record="updateRecord"
              @delete-record="deleteRecord"
              @add-record="addRecord"
              @add-field="() => showAddFieldDialog = true"
              @sort="handleSort"
            />
          </div>
        </div>
      </div>

      <!-- Regular Table View -->
      <div v-else>
        <SimpleTable 
          :records="filteredAndSortedRecords" 
          :visible-fields="visibleFields"
          :row-height="rowHeight"
          :color-by="colorBy"
          :edit-records-inline="editRecordsInline"
          :add-delete-records-inline="addDeleteRecordsInline"
          :current-sort="sortBy"
          :sort-order="sortOrder"
          @update-record="updateRecord"
          @delete-record="deleteRecord"
          @add-record="addRecord"
          @add-field="showAddFieldDialog = true"
          @sort="handleSort"
        />
      </div>
    </div>

    <!-- Modal Side Drawer -->
    <Transition name="drawer">
      <div 
        v-if="showSideDrawer" 
        class="fixed inset-0 z-50 flex justify-end"
        @click.self="showSideDrawer = false"
      >
        <!-- Backdrop -->
        <div class="absolute inset-0 bg-black bg-opacity-25"></div>
        
        <!-- Drawer Panel -->
        <div class="relative w-80 h-full bg-white shadow-xl overflow-y-auto">
          <!-- Close Button -->
          <div class="sticky top-0 bg-white border-b border-gray-200 px-4 py-3 flex items-center justify-between z-10">
            <h2 class="text-lg font-medium text-gray-900">Table Settings</h2>
            <Button 
              variant="ghost" 
              size="icon" 
              class="h-8 w-8"
              @click="showSideDrawer = false"
            >
              <span class="material-icons text-gray-500">close</span>
            </Button>
          </div>
          
          <div class="p-4 space-y-6">
            <!-- Hierarchy Section -->
            <div>
              <h3 class="font-medium text-gray-900 mb-3">Hierarchy</h3>
              <div class="space-y-2">
                <div class="flex items-center justify-between">
                  <span class="text-sm text-gray-600">Levels</span>
                  <div class="flex items-center space-x-2">
                    <Select v-model="hierarchyLevels">
                      <SelectTrigger class="h-7 w-20 text-sm">
                        <SelectValue />
                      </SelectTrigger>
                      <SelectContent>
                        <SelectItem value="1">1 level</SelectItem>
                        <SelectItem value="2">2 levels</SelectItem>
                        <SelectItem value="3">3 levels</SelectItem>
                      </SelectContent>
                    </Select>
                    <Button variant="ghost" size="icon" class="h-5 w-5">
                      <span class="material-icons text-gray-400 text-sm">settings</span>
                    </Button>
                  </div>
                </div>
              </div>
            </div>

            <!-- Data Section -->
            <div>
              <h3 class="font-medium text-gray-900 mb-3">Data</h3>
              <div class="space-y-3">
                <div>
                  <label class="text-sm text-gray-600 block mb-1">Sort by</label>
                  <Select v-model="sortBy" @update:model-value="handleSort(sortBy, sortOrder)">
                    <SelectTrigger class="h-8 text-sm">
                      <SelectValue :placeholder="sortBy ? `1 sort (${sortBy})` : '1 sort'" />
                    </SelectTrigger>
                    <SelectContent>
                      <SelectItem value="name">Name</SelectItem>
                      <SelectItem value="status">Status</SelectItem>
                      <SelectItem value="assignee">Assignee</SelectItem>
                      <SelectItem value="user">User</SelectItem>
                    </SelectContent>
                  </Select>
                </div>
                <div>
                  <label class="text-sm text-gray-600 block mb-1">Group by</label>
                  <Select v-model="groupBy" @update:model-value="handleGroup(groupBy)">
                    <SelectTrigger class="h-8 text-sm">
                      <SelectValue />
                    </SelectTrigger>
                    <SelectContent>
                      <SelectItem value="none">None</SelectItem>
                      <SelectItem value="status">Status</SelectItem>
                      <SelectItem value="assignee">Assignee</SelectItem>
                    </SelectContent>
                  </Select>
                </div>
                <div>
                  <label class="text-sm text-gray-600 block mb-1">Prefix field</label>
                  <Select v-model="prefixField">
                    <SelectTrigger class="h-8 text-sm">
                      <SelectValue />
                    </SelectTrigger>
                    <SelectContent>
                      <SelectItem value="none">None</SelectItem>
                      <SelectItem value="name">Name</SelectItem>
                    </SelectContent>
                  </Select>
                </div>
                <div>
                  <div class="flex items-center justify-between mb-2">
                    <span class="text-sm text-gray-600">Fields</span>
                    <div class="flex items-center space-x-2">
                      <span class="text-sm text-gray-900">{{ visibleFieldsCount }} visible</span>
                      <Button 
                        variant="ghost" 
                        size="icon" 
                        class="h-5 w-5"
                        @click="showAddFieldDialog = true"
                      >
                        <span class="material-icons text-gray-400 text-sm">add</span>
                      </Button>
                    </div>
                  </div>
                  <!-- Field Visibility Checkboxes -->
                  <div class="space-y-2 max-h-32 overflow-y-auto">
                    <div v-for="field in allFields" :key="field.key" class="flex items-center space-x-2">
                      <Checkbox 
                        :checked="visibleFields.includes(field.key)"
                        @update:checked="toggleFieldVisibility(field.key, $event)"
                      />
                      <span class="text-sm text-gray-700">{{ field.label }}</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- Appearance Section -->
            <div>
              <h3 class="font-medium text-gray-900 mb-3">Appearance</h3>
              <div class="space-y-3">
                <div>
                  <div class="flex items-center justify-between mb-2">
                    <span class="text-sm text-gray-600">Color by</span>
                    <div class="flex items-center space-x-2">
                      <Select v-model="colorBy">
                        <SelectTrigger class="h-7 w-20 text-sm">
                          <SelectValue />
                        </SelectTrigger>
                        <SelectContent>
                          <SelectItem value="none">None</SelectItem>
                          <SelectItem value="status">Status</SelectItem>
                        </SelectContent>
                      </Select>
                      <Button variant="ghost" size="icon" class="h-5 w-5">
                        <span class="material-icons text-gray-400 text-sm">settings</span>
                      </Button>
                    </div>
                  </div>
                </div>
                <div>
                  <div class="flex items-center justify-between mb-2">
                    <span class="text-sm text-gray-600">Field text color</span>
                    <div class="flex items-center space-x-2">
                      <Select v-model="fieldTextColor">
                        <SelectTrigger class="h-7 w-20 text-sm">
                          <SelectValue />
                        </SelectTrigger>
                        <SelectContent>
                          <SelectItem value="none">None</SelectItem>
                          <SelectItem value="status">Status</SelectItem>
                        </SelectContent>
                      </Select>
                      <Button variant="ghost" size="icon" class="h-5 w-5">
                        <span class="material-icons text-gray-400 text-sm">settings</span>
                      </Button>
                    </div>
                  </div>
                </div>
                <div>
                  <div class="flex items-center justify-between mb-2">
                    <span class="text-sm text-gray-600">Row height</span>
                    <div class="flex space-x-1">
                      <Button 
                        variant="ghost" 
                        size="icon" 
                        :class="['h-6 w-6', rowHeight === 'compact' ? 'bg-gray-100' : '']"
                        @click="rowHeight = 'compact'"
                      >
                        <span class="material-icons text-gray-600 text-sm">view_headline</span>
                      </Button>
                      <Button 
                        variant="ghost" 
                        size="icon" 
                        :class="['h-6 w-6', rowHeight === 'medium' ? 'bg-gray-100' : '']"
                        @click="rowHeight = 'medium'"
                      >
                        <span class="material-icons text-gray-600 text-sm">reorder</span>
                      </Button>
                      <Button 
                        variant="ghost" 
                        size="icon" 
                        :class="['h-6 w-6', rowHeight === 'tall' ? 'bg-gray-100' : '']"
                        @click="rowHeight = 'tall'"
                      >
                        <span class="material-icons text-gray-600 text-sm">format_line_spacing</span>
                      </Button>
                      <Button 
                        variant="ghost" 
                        size="icon" 
                        :class="['h-6 w-6', rowHeight === 'extra-tall' ? 'bg-gray-100' : '']"
                        @click="rowHeight = 'extra-tall'"
                      >
                        <span class="material-icons text-gray-600 text-sm">expand</span>
                      </Button>
                    </div>
                  </div>
                </div>
                <div class="flex items-center justify-between">
                  <span class="text-sm text-gray-600">Show field descriptions</span>
                  <Switch v-model:checked="showFieldDescriptions" />
                </div>
                <div class="flex items-center justify-between">
                  <span class="text-sm text-gray-600">Collapse all by default</span>
                  <Switch v-model:checked="collapseAllByDefault" />
                </div>
              </div>
            </div>

            <!-- User Actions Section -->
            <div>
              <h3 class="font-medium text-gray-900 mb-3">User Actions</h3>
              <div class="space-y-3">
                <div class="flex items-center justify-between">
                  <span class="text-sm text-gray-600">Edit records inline</span>
                  <Switch 
                    v-model:checked="editRecordsInline"
                    size="sm"
                  />
                </div>
                <div class="flex items-center justify-between">
                  <span class="text-sm text-gray-600">Add/delete records inline</span>
                  <Switch 
                    v-model:checked="addDeleteRecordsInline"
                    size="sm"
                  />
                </div>
                <div class="flex items-center justify-between">
                  <span class="text-sm text-gray-600">Click into record details</span>
                  <Switch 
                    v-model:checked="clickIntoRecordDetails"
                    size="sm"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </Transition>

    <!-- Add Field Dialog -->
    <AddFieldDialog 
      v-model:open="showAddFieldDialog"
      @create-field="addField"
    />

    <!-- Add Record Dialog -->
    <AddRecordDialog 
      v-model:open="showAddRecordDialog"
      :fields="allFields"
      :visible-fields="visibleFields"
      :table-name="'Table 1'"
      @create-record="createRecord"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { Checkbox } from './ui/checkbox'
import { Button } from './ui/button'
import { Switch } from './ui/switch'
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from './ui/select'
import SimpleTable from './SimpleTable.vue'
import AddFieldDialog from './AddFieldDialog.vue'
import AddRecordDialog from './AddRecordDialog.vue'

interface Attachment {
  name: string
  type: string
  file?: File
}

interface TableRecord {
  id: number
  name: string
  assignee?: {
    name: string
    avatar?: string
  }
  status?: string
  attachments?: Attachment[]
  user?: string
  createdAt: Date
  updatedAt: Date
}

// State management
const showSideDrawer = ref(false)
const showAddFieldDialog = ref(false)
const showAddRecordDialog = ref(false)
const searchQuery = ref('')
const currentFilters = ref<{ status: string[], assignee: string[] }>({ status: [], assignee: [] })

// Data controls
const sortBy = ref('name')
const sortOrder = ref<'asc' | 'desc'>('asc')
const groupBy = ref('none')
const prefixField = ref('none')
const hierarchyLevels = ref('1')

// Appearance controls
const colorBy = ref('none')
const fieldTextColor = ref('none')
const rowHeight = ref<'compact' | 'medium' | 'tall' | 'extra-tall'>('compact')
const showFieldDescriptions = ref(true)
const collapseAllByDefault = ref(false)

// User action controls
const editRecordsInline = ref(true)
const addDeleteRecordsInline = ref(false)
const clickIntoRecordDetails = ref(true)

// Field management
const allFields = ref([
  { key: 'name', label: 'Name', type: 'text', icon: 'text_fields' },
  { key: 'assignee', label: 'Assignee', type: 'user', icon: 'person' },
  { key: 'status', label: 'Status', type: 'select', icon: 'radio_button_checked' },
  { key: 'attachmentSummary', label: 'Attachment Summary', type: 'formula', icon: 'summarize' },
  { key: 'attachments', label: 'Attachments', type: 'attachment', icon: 'attachment' },
  { key: 'user', label: 'User', type: 'text', icon: 'person' },
])

const visibleFields = ref(['name', 'assignee', 'status', 'attachmentSummary', 'attachments', 'user'])
const collapsedGroups = ref(new Set<string>())

// Sample data
const records = ref<TableRecord[]>([
  { 
    id: 1, 
    name: '', 
    createdAt: new Date('2024-01-01'), 
    updatedAt: new Date('2024-01-01') 
  },
  { 
    id: 2, 
    name: '', 
    createdAt: new Date('2024-01-02'), 
    updatedAt: new Date('2024-01-02') 
  },
  { 
    id: 3, 
    name: 'shoaib', 
    assignee: { name: 'Shoaib Rehman' }, 
    status: 'Todo',
    user: 'shoaib notes',
    attachments: [
      { name: 'sample.jpg', type: 'image' },
      { name: 'document.pdf', type: 'document' }
    ],
    createdAt: new Date('2024-01-03'), 
    updatedAt: new Date('2024-01-03') 
  },
  { 
    id: 4, 
    name: 'Complete design', 
    assignee: { name: 'John Doe' }, 
    status: 'In Progress',
    user: 'design team',
    createdAt: new Date('2024-01-04'), 
    updatedAt: new Date('2024-01-04') 
  },
  { 
    id: 5, 
    name: 'Review code', 
    assignee: { name: 'Jane Smith' }, 
    status: 'Done',
    user: 'review notes',
    createdAt: new Date('2024-01-05'), 
    updatedAt: new Date('2024-01-05') 
  }
])

// Computed properties
const visibleFieldsCount = computed(() => visibleFields.value.length)

const filteredAndSortedRecords = computed(() => {
  let filtered = [...records.value]

  // Apply search filter
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase()
    filtered = filtered.filter(record => 
      record.name.toLowerCase().includes(query) ||
      record.assignee?.name.toLowerCase().includes(query) ||
      record.status?.toLowerCase().includes(query) ||
      record.user?.toLowerCase().includes(query)
    )
  }

  // Apply filters
  if (currentFilters.value.status.length > 0) {
    filtered = filtered.filter(record => 
      record.status && currentFilters.value.status.includes(record.status)
    )
  }

  if (currentFilters.value.assignee.length > 0) {
    filtered = filtered.filter(record => 
      record.assignee && currentFilters.value.assignee.includes(record.assignee.name)
    )
  }

  // Apply sorting
  filtered.sort((a, b) => {
    let aValue: any = a[sortBy.value as keyof TableRecord]
    let bValue: any = b[sortBy.value as keyof TableRecord]

    // Handle special cases
    if (sortBy.value === 'assignee') {
      aValue = a.assignee?.name || ''
      bValue = b.assignee?.name || ''
    }

    // Handle dates
    if (aValue instanceof Date && bValue instanceof Date) {
      return sortOrder.value === 'asc' 
        ? aValue.getTime() - bValue.getTime()
        : bValue.getTime() - aValue.getTime()
    }

    // Handle strings
    const aStr = String(aValue || '').toLowerCase()
    const bStr = String(bValue || '').toLowerCase()
    
    if (sortOrder.value === 'asc') {
      return aStr.localeCompare(bStr)
    } else {
      return bStr.localeCompare(aStr)
    }
  })

  return filtered
})

const groupedRecords = computed(() => {
  if (groupBy.value === 'none') return []

  const groups = new Map<string, TableRecord[]>()
  
  filteredAndSortedRecords.value.forEach(record => {
    let groupKey = ''
    
    if (groupBy.value === 'status') {
      groupKey = record.status || 'No Status'
    } else if (groupBy.value === 'assignee') {
      groupKey = record.assignee?.name || 'Unassigned'
    }
    
    if (!groups.has(groupKey)) {
      groups.set(groupKey, [])
    }
    groups.get(groupKey)!.push(record)
  })

  return Array.from(groups.entries()).map(([key, records]) => ({
    key,
    records
  })).sort((a, b) => a.key.localeCompare(b.key))
})

// Methods
function toggleFieldVisibility(fieldKey: string, checked: boolean) {
  if (checked) {
    if (!visibleFields.value.includes(fieldKey)) {
      visibleFields.value.push(fieldKey)
    }
  } else {
    visibleFields.value = visibleFields.value.filter(key => key !== fieldKey)
  }
}

function toggleGroupCollapse(groupKey: string) {
  if (collapsedGroups.value.has(groupKey)) {
    collapsedGroups.value.delete(groupKey)
  } else {
    collapsedGroups.value.add(groupKey)
  }
}

function addRecord() {
  showAddRecordDialog.value = true
}

function createRecord(recordData: any) {
  const newId = Math.max(...records.value.map(r => r.id)) + 1
  const newRecord: TableRecord = {
    id: newId,
    name: recordData.name || '',
    assignee: recordData.assignee,
    status: recordData.status,
    attachments: recordData.attachments,
    user: recordData.user,
    createdAt: new Date(),
    updatedAt: new Date(),
    ...recordData
  }
  records.value.push(newRecord)
}

function updateRecord(recordId: number, updates: Partial<TableRecord>) {
  console.log('Updating record:', recordId, 'with updates:', updates)
  const index = records.value.findIndex(r => r.id === recordId)
  if (index !== -1) {
    records.value[index] = {
      ...records.value[index],
      ...updates,
      updatedAt: new Date()
    }
    console.log('Updated record:', records.value[index])
  } else {
    console.log('Record not found:', recordId)
  }
}

function addField(field: any) {
  console.log('Adding field:', field)
  
  allFields.value.push({
    key: field.key,
    label: field.label,
    type: field.type,
    icon: field.icon
  })
  
  visibleFields.value.push(field.key)
  showAddFieldDialog.value = false
}

function deleteRecord(recordId: number) {
  records.value = records.value.filter(r => r.id !== recordId)
}

function handleSort(field: string, order: 'asc' | 'desc') {
  sortBy.value = field
  sortOrder.value = order
}

function handleSearch(query: string) {
  searchQuery.value = query
}

function handleFilter(filters: any) {
  currentFilters.value = filters
}

function handleGroup(field: string) {
  groupBy.value = field
}

function toggleSideDrawer() {
  showSideDrawer.value = !showSideDrawer.value
}

// Expose methods to parent
defineExpose({
  addRecord,
  toggleSideDrawer,
  handleSearch,
  handleFilter,
  handleSort,
  handleGroup
})
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');

/* Drawer transition animations */
.drawer-enter-active,
.drawer-leave-active {
  transition: all 0.3s ease-in-out;
}

.drawer-enter-from {
  opacity: 0;
}

.drawer-enter-to {
  opacity: 1;
}

.drawer-leave-from {
  opacity: 1;
}

.drawer-leave-to {
  opacity: 0;
}

/* Drawer panel slide animation */
.drawer-enter-active .relative,
.drawer-leave-active .relative {
  transition: transform 0.3s ease-in-out;
}

.drawer-enter-from .relative {
  transform: translateX(100%);
}

.drawer-enter-to .relative {
  transform: translateX(0);
}

.drawer-leave-from .relative {
  transform: translateX(0);
}

.drawer-leave-to .relative {
  transform: translateX(100%);
}
</style> 