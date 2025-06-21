<template>
  <Dialog v-model:open="isOpen">
    <DialogContent class="max-w-lg max-h-[80vh] overflow-y-auto">
      <DialogHeader>
        <DialogTitle>{{ tableName }} Form</DialogTitle>
        <DialogDescription>
          Add a description
        </DialogDescription>
      </DialogHeader>
      
      <div class="space-y-6">
        <!-- New Group Section -->
        <div class="bg-gray-50 p-3 rounded-lg">
          <h3 class="font-medium text-gray-900 mb-4">New Group</h3>
          
          <!-- Dynamic Fields -->
          <div class="space-y-4">
            <!-- Name Field -->
            <div v-if="hasField('name')">
              <label class="text-sm font-medium text-gray-700 block mb-2">Name</label>
              <Input 
                v-model="formData.name"
                placeholder="Enter name..."
                class="w-full"
              />
            </div>

            <!-- Notes Field (User field) -->
            <div v-if="hasField('user')">
              <label class="text-sm font-medium text-gray-700 block mb-2">Notes</label>
              <textarea 
                v-model="formData.user"
                placeholder="Add notes..."
                class="w-full min-h-[80px] px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent resize-none"
              />
            </div>

            <!-- Assignee Field -->
            <div v-if="hasField('assignee')">
              <label class="text-sm font-medium text-gray-700 block mb-2">Assignee</label>
              <Select v-model="formData.assignee">
                <SelectTrigger class="w-full">
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
            </div>

            <!-- Status Field -->
            <div v-if="hasField('status')">
              <label class="text-sm font-medium text-gray-700 block mb-2">Status</label>
              <Select v-model="formData.status">
                <SelectTrigger class="w-full">
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
            </div>

            <!-- Attachments Field -->
            <div v-if="hasField('attachments')">
              <label class="text-sm font-medium text-gray-700 block mb-2">Attachments</label>
              <div 
                class="border-2 border-dashed border-gray-300 rounded-lg p-6 text-center hover:border-gray-400 transition-colors cursor-pointer"
                @click="triggerFileInput"
                @dragover.prevent
                @drop.prevent="handleFileDrop"
              >
                <span class="material-icons text-gray-400 text-2xl mb-2">cloud_upload</span>
                <p class="text-sm text-gray-600">Drop files here or 
                  <span class="text-blue-600 underline">browse</span>
                </p>
              </div>
              <input 
                ref="fileInput"
                type="file" 
                multiple 
                class="hidden" 
                accept="image/*,application/pdf,.doc,.docx"
                @change="handleFileSelect"
              />
              
              <!-- Selected Files Preview -->
              <div v-if="selectedFiles.length > 0" class="mt-3 space-y-2">
                <h4 class="text-sm font-medium text-gray-700">Selected files:</h4>
                <div class="grid grid-cols-2 gap-2">
                  <div v-for="(file, index) in selectedFiles" :key="index" class="relative group">
                    <!-- Image Preview -->
                    <div v-if="file.type === 'image'" class="relative">
                      <img 
                        :src="getImagePreview(file.file)"
                        :alt="file.name"
                        class="w-full h-20 object-cover rounded border border-gray-200"
                      />
                      <div class="absolute inset-0 bg-black bg-opacity-0 group-hover:bg-opacity-20 transition-all duration-200 rounded flex items-center justify-center">
                        <Button 
                          variant="ghost" 
                          size="icon" 
                          class="h-6 w-6 bg-white bg-opacity-80 hover:bg-opacity-100 opacity-0 group-hover:opacity-100 transition-opacity"
                          @click="removeFile(index)"
                        >
                          <span class="material-icons text-xs text-gray-700">close</span>
                        </Button>
                      </div>
                      <div class="absolute bottom-0 left-0 right-0 bg-black bg-opacity-60 text-white text-xs p-1 rounded-b truncate">
                        {{ file.name }}
                      </div>
                    </div>
                    
                    <!-- File Icon -->
                    <div v-else class="relative">
                      <div class="w-full h-20 rounded border border-gray-200 flex flex-col items-center justify-center p-2" :class="getFileBackgroundColor(file.type)">
                        <span class="material-icons text-white text-lg">{{ getFileIcon(file.type) }}</span>
                        <span class="text-xs text-white mt-1 text-center truncate w-full">{{ getFileExtension(file.name) }}</span>
                      </div>
                      <Button 
                        variant="ghost" 
                        size="icon" 
                        class="absolute top-1 right-1 h-5 w-5 bg-white bg-opacity-80 hover:bg-opacity-100 opacity-0 group-hover:opacity-100 transition-opacity"
                        @click="removeFile(index)"
                      >
                        <span class="material-icons text-xs text-gray-700">close</span>
                      </Button>
                      <div class="absolute bottom-0 left-0 right-0 bg-black bg-opacity-60 text-white text-xs p-1 rounded-b truncate">
                        {{ file.name }}
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- Dynamic Custom Fields -->
            <div v-for="field in customFields" :key="field.key" class="space-y-2">
              <label class="text-sm font-medium text-gray-700 block">{{ field.label }}</label>
              
              <!-- Text Field -->
              <Input 
                v-if="field.type === 'text'"
                v-model="formData[field.key]"
                :placeholder="`Enter ${field.label.toLowerCase()}...`"
                class="w-full"
              />
              
              <!-- Number Field -->
              <Input 
                v-else-if="field.type === 'number'"
                v-model="formData[field.key]"
                type="number"
                :placeholder="`Enter ${field.label.toLowerCase()}...`"
                class="w-full"
              />
              
              <!-- Date Field -->
              <Input 
                v-else-if="field.type === 'date'"
                v-model="formData[field.key]"
                type="datetime-local"
                class="w-full"
              />
              
              <!-- Checkbox Field -->
              <div v-else-if="field.type === 'checkbox'" class="flex items-center space-x-2">
                <Checkbox 
                  v-model:checked="formData[field.key]"
                  :id="field.key"
                />
                <label :for="field.key" class="text-sm text-gray-700">{{ field.label }}</label>
              </div>
              
              <!-- URL Field -->
              <Input 
                v-else-if="field.type === 'url'"
                v-model="formData[field.key]"
                type="url"
                :placeholder="`Enter ${field.label.toLowerCase()}...`"
                class="w-full"
              />
              
              <!-- Email Field -->
              <Input 
                v-else-if="field.type === 'email'"
                v-model="formData[field.key]"
                type="email"
                :placeholder="`Enter ${field.label.toLowerCase()}...`"
                class="w-full"
              />
              
              <!-- Select Field -->
              <Select 
                v-else-if="field.type === 'select'"
                v-model="formData[field.key]"
              >
                <SelectTrigger class="w-full">
                  <SelectValue :placeholder="`Select ${field.label.toLowerCase()}...`" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem 
                    v-for="option in field.options" 
                    :key="option.value" 
                    :value="option.value"
                  >
                    {{ option.value }}
                  </SelectItem>
                </SelectContent>
              </Select>
            </div>
          </div>
        </div>
      </div>

      <DialogFooter class="flex items-center justify-between">
        <Button variant="ghost" @click="clearForm" class="text-blue-600">
          <span class="material-icons text-sm mr-1">refresh</span>
          Clear form
        </Button>
        <div class="flex space-x-2">
          <Button variant="outline" @click="cancel">Cancel</Button>
          <Button @click="createRecord" class="bg-gray-800 hover:bg-gray-900">
            Create
          </Button>
        </div>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
} from './ui/dialog'
import { Button } from './ui/button'
import { Input } from './ui/input'
import { Checkbox } from './ui/checkbox'
import { Avatar, AvatarFallback } from './ui/avatar'
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from './ui/select'

interface Field {
  key: string
  label: string
  type: string
  icon: string
  options?: { value: string }[]
}

interface Attachment {
  name: string
  type: string
  file: File
}

const props = defineProps<{
  open: boolean
  fields: Field[]
  visibleFields: string[]
  tableName?: string
}>()

const emit = defineEmits<{
  'update:open': [value: boolean]
  'create-record': [record: any]
}>()

// Local state
const formData = ref<Record<string, any>>({})
const selectedFiles = ref<Attachment[]>([])
const fileInput = ref<HTMLInputElement>()

const isOpen = computed({
  get: () => props.open,
  set: (value: boolean) => emit('update:open', value)
})

// Computed properties
const customFields = computed(() => {
  return props.fields.filter(field => 
    props.visibleFields.includes(field.key) && 
    !['name', 'assignee', 'status', 'attachments', 'user', 'attachmentSummary'].includes(field.key)
  )
})

// Watch for when dialog closes to reset form
watch(() => props.open, (newValue) => {
  if (!newValue) {
    resetForm()
  }
})

// Methods
function hasField(fieldKey: string): boolean {
  return props.visibleFields.includes(fieldKey)
}

function resetForm() {
  formData.value = {}
  selectedFiles.value = []
}

function clearForm() {
  resetForm()
}

function getFileIcon(type: string): string {
  if (type.startsWith('image/')) return 'image'
  if (type.includes('pdf')) return 'picture_as_pdf'
  if (type.includes('document') || type.includes('word')) return 'description'
  if (type.includes('sheet') || type.includes('excel')) return 'table_chart'
  return 'attachment'
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

function getFileExtension(filename: string): string {
  const ext = filename.split('.').pop()?.toUpperCase()
  return ext || 'FILE'
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
  const newFiles = files.map(file => ({
    name: file.name,
    type: file.type,
    file: file
  }))
  selectedFiles.value.push(...newFiles)
}

function removeFile(index: number) {
  selectedFiles.value.splice(index, 1)
}

function createRecord() {
  const newRecord: any = {
    ...formData.value
  }

  // Handle assignee
  if (formData.value.assignee) {
    const assigneeMap: Record<string, { name: string }> = {
      'shoaib-rehman': { name: 'Shoaib Rehman' },
      'john-doe': { name: 'John Doe' },
      'jane-smith': { name: 'Jane Smith' }
    }
    newRecord.assignee = assigneeMap[formData.value.assignee]
  }

  // Handle attachments
  if (selectedFiles.value.length > 0) {
    newRecord.attachments = selectedFiles.value
  }

  console.log('Creating record:', newRecord)
  emit('create-record', newRecord)
  emit('update:open', false)
}

function cancel() {
  emit('update:open', false)
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 