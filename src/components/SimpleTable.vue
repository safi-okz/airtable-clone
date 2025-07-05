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
        :style="{ cursor: clickIntoRecordDetails ? 'pointer' : 'default' }"
        @click="clickIntoRecordDetails ? openRecordDetails(record) : null"
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
          class="border-r border-gray-200 relative"
        >
          <!-- Name Field -->
          <div v-if="field.key === 'name'" class="text-sm">
            <div 
              :class="[
                'w-full rounded-sm transition-all duration-200',
                editingCell === `${record.id}-name` && editRecordsInline ? 'ring-2 ring-blue-500 bg-white shadow-sm' : ''
              ]"
            >
              <!-- Editable input when editing is enabled -->
              <input 
                v-if="editRecordsInline && (editingCell === `${record.id}-name` || record.name)" 
                v-model="record.name"
                :class="[
                  'w-full bg-transparent border-none outline-none text-gray-900 placeholder-gray-400 px-2 py-1',
                  editingCell === `${record.id}-name` ? 'bg-white' : ''
                ]"
                :placeholder="record.name ? '' : 'Blank...'"
                @blur="saveField(record.id, 'name', record.name)"
                @keydown.enter="saveField(record.id, 'name', record.name)"
                @focus="editingCell = `${record.id}-name`"
              />
              <!-- Read-only display when editing is disabled -->
              <span 
                v-else-if="!editRecordsInline && record.name"
                class="text-gray-900 px-2 py-1 block"
              >
                {{ record.name }}
              </span>
              <!-- Clickable placeholder when editing is enabled but no name -->
              <span 
                v-else-if="editRecordsInline && !record.name"
                class="text-gray-400 cursor-pointer hover:bg-gray-50 px-2 py-1 rounded-sm block" 
                @click="startEditing(record.id, 'name')"
              >
                Blank...
              </span>
              <!-- Static placeholder when editing is disabled and no name -->
              <span 
                v-else
                class="text-gray-400 px-2 py-1 block"
              >
                Blank...
              </span>
            </div>
          </div>

          <!-- Assignee Field -->
          <div v-else-if="field.key === 'assignee'" class="text-sm">
            <!-- Show assignee when exists -->
            <div v-if="record.assignee" class="flex items-center space-x-2 px-2 py-1">
              <Avatar class="w-6 h-6 text-xs">
                <AvatarFallback :class="getAvatarColor(record.assignee.name)">
                  {{ getInitials(record.assignee.name) }}
                </AvatarFallback>
              </Avatar>
              <span class="text-gray-900">{{ record.assignee.name }}</span>
              <Button 
                v-if="editRecordsInline"
                variant="ghost" 
                size="icon" 
                class="h-4 w-4 opacity-0 group-hover:opacity-100 hover:bg-gray-200"
                @click="clearAssignee(record.id)"
              >
                <span class="material-icons text-xs text-gray-400">close</span>
              </Button>
            </div>
            <!-- Select dropdown only when editing is enabled and no assignee -->
            <Select v-else-if="editRecordsInline" @update:model-value="(value) => updateAssignee(record.id, value)">
              <SelectTrigger class="h-8 text-sm border-none bg-transparent hover:bg-gray-50 focus:ring-2 focus:ring-blue-500">
                <SelectValue placeholder="Select assignee..." />
              </SelectTrigger>
              <SelectContent>
                <SelectItem value="shoaib-rehman">
                  <div class="flex items-center space-x-2">
                    <Avatar class="w-5 h-5 text-xs">
                      <AvatarFallback class="bg-teal-400 text-white">SR</AvatarFallback>
                    </Avatar>
                    <span>Shoaib Rehman</span>
                  </div>
                </SelectItem>
                <SelectItem value="john-doe">
                  <div class="flex items-center space-x-2">
                    <Avatar class="w-5 h-5 text-xs">
                      <AvatarFallback class="bg-blue-400 text-white">JD</AvatarFallback>
                    </Avatar>
                    <span>John Doe</span>
                  </div>
                </SelectItem>
                <SelectItem value="jane-smith">
                  <div class="flex items-center space-x-2">
                    <Avatar class="w-5 h-5 text-xs">
                      <AvatarFallback class="bg-purple-400 text-white">JS</AvatarFallback>
                    </Avatar>
                    <span>Jane Smith</span>
                  </div>
                </SelectItem>
              </SelectContent>
            </Select>
            <!-- Static placeholder when editing is disabled -->
            <span v-else class="text-gray-400 px-2 py-1">–</span>
          </div>

          <!-- Status Field -->
          <div v-else-if="field.key === 'status'" class="text-sm">
            <!-- Show status badge when exists -->
            <div v-if="record.status" class="px-2 py-1 flex items-center justify-between group">
              <span 
                :class="getStatusBadgeClass(record.status)"
                class="px-2 py-1 rounded-full text-xs font-medium"
              >
                {{ record.status }}
              </span>
              <Button 
                v-if="editRecordsInline"
                variant="ghost" 
                size="icon" 
                class="h-4 w-4 opacity-0 group-hover:opacity-100 hover:bg-gray-200"
                @click="clearStatus(record.id)"
              >
                <span class="material-icons text-xs text-gray-400">close</span>
              </Button>
            </div>
            <!-- Select dropdown only when editing is enabled and no status -->
            <Select v-else-if="editRecordsInline" @update:model-value="(value) => updateStatus(record.id, value)">
              <SelectTrigger class="h-8 text-sm border-none bg-transparent hover:bg-gray-50 focus:ring-2 focus:ring-blue-500">
                <SelectValue placeholder="Select status..." />
              </SelectTrigger>
              <SelectContent>
                <SelectItem value="Todo">
                  <span class="px-2 py-1 rounded-full text-xs font-medium bg-red-100 text-red-800">
                    Todo
                  </span>
                </SelectItem>
                <SelectItem value="In Progress">
                  <span class="px-2 py-1 rounded-full text-xs font-medium bg-yellow-100 text-yellow-800">
                    In Progress
                  </span>
                </SelectItem>
                <SelectItem value="Done">
                  <span class="px-2 py-1 rounded-full text-xs font-medium bg-green-100 text-green-800">
                    Done
                  </span>
                </SelectItem>
              </SelectContent>
            </Select>
            <!-- Static placeholder when editing is disabled -->
            <span v-else class="text-gray-400 px-2 py-1">–</span>
          </div>

          <!-- Attachment Summary Field -->
          <div v-else-if="field.key === 'attachmentSummary'" class="text-sm text-gray-400 px-2 py-1">
            <span class="flex items-center space-x-1">
              <span>Required field(s) are...</span>
              <span class="material-icons text-gray-400 text-sm">info</span>
            </span>
          </div>

          <!-- Attachments Field -->
          <div v-else-if="field.key === 'attachments'" class="text-sm px-2 py-1">
            <div v-if="record.attachments && record.attachments.length > 0" class="flex items-center space-x-2">
              <div class="flex items-center space-x-1">
                <div 
                  v-for="(attachment, index) in record.attachments.slice(0, 4)" 
                  :key="index"
                  class="relative group cursor-pointer"
                  @mouseenter="showPreview(attachment, $event)"
                  @mouseleave="hidePreview"
                >
                  <!-- Image Preview -->
                  <div 
                    v-if="attachment.type === 'image'"
                    class="w-8 h-8 rounded border border-gray-200 shadow-sm overflow-hidden bg-gray-100 hover:border-gray-300 transition-colors"
                  >
                    <img 
                      v-if="attachment.file"
                      :src="getImagePreview(attachment.file)"
                      :alt="attachment.name"
                      class="w-full h-full object-cover"
                    />
                    <div v-else class="w-full h-full flex items-center justify-center">
                      <span class="material-icons text-gray-400 text-sm">image</span>
                    </div>
                  </div>
                  
                  <!-- File Icon -->
                  <div 
                    v-else
                    class="w-8 h-8 rounded border border-gray-200 shadow-sm flex items-center justify-center hover:border-gray-300 transition-colors"
                    :class="getFileBackgroundColor(attachment.type)"
                  >
                    <span class="material-icons text-white text-sm">{{ getAttachmentIcon(attachment.type) }}</span>
                  </div>
                </div>
              </div>
              
              <span v-if="record.attachments.length > 4" class="text-xs text-gray-500 ml-1">
                +{{ record.attachments.length - 4 }}
              </span>
              
              <Button 
                v-if="editRecordsInline"
                variant="ghost" 
                size="icon" 
                class="h-5 w-5 opacity-0 group-hover:opacity-100 hover:bg-gray-200 ml-2"
                @click="openAttachmentDialog(record.id)"
              >
                <span class="material-icons text-xs text-gray-400">add</span>
              </Button>
            </div>
            <div v-else-if="editRecordsInline" class="flex items-center space-x-2">
              <Button 
                variant="ghost" 
                size="sm" 
                class="h-6 px-2 text-gray-400 hover:text-gray-600 hover:bg-gray-100 border border-dashed border-gray-300 hover:border-gray-400"
                @click="openAttachmentDialog(record.id)"
              >
                <span class="material-icons text-sm mr-1">attach_file</span>
                <span class="text-xs">Add</span>
              </Button>
            </div>
            <span v-else class="text-gray-400">–</span>
          </div>

          <!-- User Field -->
          <div v-else-if="field.key === 'user'" class="text-sm">
            <div 
              :class="[
                'w-full rounded-sm transition-all duration-200',
                editingCell === `${record.id}-user` && editRecordsInline ? 'ring-2 ring-blue-500 bg-white shadow-sm' : ''
              ]"
            >
              <!-- Editable input when editing is enabled -->
              <input 
                v-if="editRecordsInline && (editingCell === `${record.id}-user` || record.user)" 
                v-model="record.user"
                :class="[
                  'w-full bg-transparent border-none outline-none text-gray-900 placeholder-gray-400 px-2 py-1',
                  editingCell === `${record.id}-user` ? 'bg-white' : ''
                ]"
                :placeholder="record.user ? '' : 'Add notes...'"
                @blur="saveField(record.id, 'user', record.user)"
                @keydown.enter="saveField(record.id, 'user', record.user)"
                @focus="editingCell = `${record.id}-user`"
              />
              <!-- Read-only display when editing is disabled -->
              <span 
                v-else-if="!editRecordsInline && record.user"
                class="text-gray-900 px-2 py-1 block"
              >
                {{ record.user }}
              </span>
              <!-- Clickable placeholder when editing is enabled but no user -->
              <span 
                v-else-if="editRecordsInline && !record.user"
                class="text-gray-400 cursor-pointer hover:bg-gray-50 px-2 py-1 rounded-sm block" 
                @click="startEditing(record.id, 'user')"
              >
                –
              </span>
              <!-- Static placeholder when editing is disabled and no user -->
              <span 
                v-else
                class="text-gray-400 px-2 py-1 block"
              >
                –
              </span>
            </div>
          </div>

          <!-- Default Field -->
          <div v-else class="text-sm px-2 py-1">
            <span class="text-gray-400">–</span>
          </div>
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

  <!-- Attachment Dialog -->
  <Dialog v-model:open="showAttachmentDialog">
    <DialogContent class="max-w-md">
      <DialogHeader>
        <DialogTitle>Add Attachments</DialogTitle>
        <DialogDescription>
          Upload files to attach to this record
        </DialogDescription>
      </DialogHeader>
      <div class="space-y-4">
        <div 
          class="border-2 border-dashed border-gray-300 rounded-lg p-6 text-center hover:border-gray-400 transition-colors cursor-pointer"
          @click="triggerFileInput"
          @dragover.prevent
          @drop.prevent="handleFileDrop"
        >
          <span class="material-icons text-gray-400 text-4xl mb-2">cloud_upload</span>
          <p class="text-sm text-gray-600">Click to upload or drag and drop</p>
          <p class="text-xs text-gray-500 mt-1">PNG, JPG, PDF up to 10MB</p>
        </div>
        <input 
          ref="fileInput"
          type="file" 
          multiple 
          class="hidden" 
          accept="image/*,application/pdf,.doc,.docx"
          @change="handleFileSelect"
        />
        <div v-if="selectedFiles.length > 0" class="space-y-2">
          <h4 class="text-sm font-medium">Selected Files:</h4>
          <div v-for="(file, index) in selectedFiles" :key="index" class="flex items-center justify-between p-2 bg-gray-50 rounded">
            <div class="flex items-center space-x-2">
              <span class="material-icons text-gray-500 text-sm">{{ getFileIcon(file.type) }}</span>
              <span class="text-sm">{{ file.name }}</span>
            </div>
            <Button variant="ghost" size="icon" class="h-5 w-5" @click="removeFile(index)">
              <span class="material-icons text-xs">close</span>
            </Button>
          </div>
        </div>
      </div>
      <DialogFooter>
        <Button variant="outline" @click="showAttachmentDialog = false">Cancel</Button>
        <Button @click="uploadAttachments" :disabled="selectedFiles.length === 0">
          Upload {{ selectedFiles.length }} file{{ selectedFiles.length !== 1 ? 's' : '' }}
        </Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
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
import { Avatar, AvatarFallback } from './ui/avatar'
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from './ui/select'
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
} from './ui/dialog'
import type { AcceptableValue } from 'reka-ui'

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

interface Field {
  key: string
  label: string
  type: string
  icon: string
}

const props = defineProps<{
  records: TableRecord[]
  visibleFields: string[]
  rowHeight: 'compact' | 'medium' | 'tall' | 'extra-tall'
  editRecordsInline?: boolean
  addDeleteRecordsInline?: boolean
  clickIntoRecordDetails?: boolean
  colorBy?: string
  currentSort?: string
  sortOrder?: 'asc' | 'desc'
}>()

const emit = defineEmits<{
  'update-record': [id: number, data: Partial<TableRecord>]
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

// Local state
const selectedRecords = ref(new Set<number>())
const editingCell = ref('')
const showAttachmentDialog = ref(false)
const selectedFiles = ref<Attachment[]>([])
const fileInput = ref<HTMLInputElement>()
const currentRecordId = ref<number>(0)
const previewTooltip = ref<HTMLDivElement>()
const showingPreview = ref(false)

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

function getRowClass(record: TableRecord) {
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

function getRowColorClass(record: TableRecord) {
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

function startEditing(recordId: number, field: string) {
  if (props.editRecordsInline) {
    editingCell.value = `${recordId}-${field}`
  }
}

function saveField(recordId: number, field: string, value: any) {
  emit('update-record', recordId, { [field]: value })
  editingCell.value = ''
}

function updateAssignee(recordId: number, value: AcceptableValue) {
  if (typeof value === 'string') {
    const assigneeMap: { [key: string]: { name: string } } = {
      'shoaib-rehman': { name: 'Shoaib Rehman' },
      'john-doe': { name: 'John Doe' },
      'jane-smith': { name: 'Jane Smith' }
    }
    
    emit('update-record', recordId, { assignee: assigneeMap[value] })
  }
}

function updateStatus(recordId: number, value: AcceptableValue) {
  if (typeof value === 'string') {
    emit('update-record', recordId, { status: value })
  }
}



function deleteRecord(recordId: number) {
  selectedRecords.value.delete(recordId)
  emit('delete-record', recordId)
}

function addRecord() {
  emit('add-record')
}

function openRecordDetails(record: TableRecord) {
  console.log('Open record details for:', record)
}

function getInitials(name: string): string {
  return name.split(' ').map(n => n[0]).join('').toUpperCase()
}

function getAvatarColor(name: string): string {
  const colors = [
    'bg-teal-400 text-white',
    'bg-blue-400 text-white',
    'bg-purple-400 text-white',
    'bg-pink-400 text-white',
    'bg-orange-400 text-white',
  ]
  const index = name.length % colors.length
  return colors[index]
}

function getStatusBadgeClass(status: string): string {
  switch (status) {
    case 'Todo':
      return 'bg-red-100 text-red-800'
    case 'In Progress':
      return 'bg-yellow-100 text-yellow-800'
    case 'Done':
      return 'bg-green-100 text-green-800'
    default:
      return 'bg-gray-100 text-gray-800'
  }
}

function getAttachmentIcon(type: string): string {
  switch (type) {
    case 'image':
      return 'image'
    case 'document':
      return 'description'
    case 'spreadsheet':
      return 'table_chart'
    case 'presentation':
      return 'slideshow'
    default:
      return 'attachment'
  }
}

function getFileIcon(type: string): string {
  switch (type) {
    case 'image':
      return 'image'
    case 'document':
      return 'description'
    case 'spreadsheet':
      return 'table_chart'
    case 'presentation':
      return 'slideshow'
    default:
      return 'attachment'
  }
}

function triggerFileInput() {
  if (fileInput.value) {
    fileInput.value.click()
  }
}

function handleFileDrop(event: DragEvent) {
  event.preventDefault()
  const dataTransfer = event.dataTransfer
  if (dataTransfer) {
    const files = Array.from(dataTransfer.files)
    processFiles(files)
  }
}

function handleFileSelect(event: Event) {
  const target = event.target as HTMLInputElement
  if (target && target.files) {
    const files = Array.from(target.files)
    processFiles(files)
  }
}

function processFiles(files: File[]) {
  console.log('Processing files:', files)
  selectedFiles.value = files.map(file => ({
    name: file.name,
    type: getFileTypeFromMime(file.type),
    file: file
  }))
  console.log('Processed files:', selectedFiles.value)
}

function getFileTypeFromMime(mimeType: string): string {
  if (mimeType.startsWith('image/')) return 'image'
  if (mimeType.includes('pdf') || mimeType.includes('document')) return 'document'
  if (mimeType.includes('sheet')) return 'spreadsheet'
  if (mimeType.includes('presentation')) return 'presentation'
  return 'document'
}

function removeFile(index: number) {
  selectedFiles.value.splice(index, 1)
}

function uploadAttachments() {
  if (selectedFiles.value.length > 0) {
    // Add attachments to the current record
    const attachments = selectedFiles.value.map(file => ({
      name: file.name,
      type: file.type,
      file: file.file
    }))
    
    console.log('Uploading attachments:', attachments)
    emit('update-record', currentRecordId.value, { attachments })
    
    // Clear the dialog
    selectedFiles.value = []
    showAttachmentDialog.value = false
  }
}

function clearAssignee(recordId: number) {
  emit('update-record', recordId, { assignee: undefined })
}

function clearStatus(recordId: number) {
  emit('update-record', recordId, { status: undefined })
}

function openAttachmentDialog(recordId: number) {
  console.log('Opening attachment dialog for record:', recordId)
  currentRecordId.value = recordId
  selectedFiles.value = []
  showAttachmentDialog.value = true
}

function getImagePreview(file: File): string {
  return URL.createObjectURL(file)
}

function getFileBackgroundColor(type: string): string {
  switch (type) {
    case 'document':
      return 'bg-blue-500'
    case 'spreadsheet':
      return 'bg-green-500'
    case 'presentation':
      return 'bg-orange-500'
    case 'image':
      return 'bg-purple-500'
    default:
      return 'bg-gray-500'
  }
}

function showPreview(attachment: Attachment, event: MouseEvent) {
  if (attachment.type === 'image' && attachment.file) {
    showingPreview.value = true
    
    // Create preview tooltip
    const tooltip = document.createElement('div')
    tooltip.className = 'fixed z-50 bg-white border border-gray-200 rounded-lg shadow-lg p-2 pointer-events-none'
    tooltip.style.left = `${event.pageX + 10}px`
    tooltip.style.top = `${event.pageY - 10}px`
    
    const img = document.createElement('img')
    img.src = getImagePreview(attachment.file)
    img.className = 'w-48 h-32 object-cover rounded'
    img.alt = attachment.name
    
    const name = document.createElement('div')
    name.className = 'text-xs text-gray-600 mt-1 text-center'
    name.textContent = attachment.name
    
    tooltip.appendChild(img)
    tooltip.appendChild(name)
    document.body.appendChild(tooltip)
    
    previewTooltip.value = tooltip
  }
}

function hidePreview() {
  if (previewTooltip.value) {
    document.body.removeChild(previewTooltip.value)
    previewTooltip.value = undefined
    showingPreview.value = false
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 