<template>
  <div class="text-sm">
    <!-- Name Field -->
    <div v-if="field.key === 'name'" class="w-full">
      <input 
        v-if="isEditing || record.name" 
        v-model="localValue"
        class="w-full bg-transparent border-none outline-none text-gray-900 placeholder-gray-400"
        :placeholder="record.name ? '' : 'Blank...'"
        :readonly="!editable"
        @blur="saveChanges"
        @keydown.enter="saveChanges"
        @focus="isEditing = true"
      />
      <span 
        v-else 
        class="text-gray-400 cursor-pointer" 
        @click="startEditing"
      >
        Blank...
      </span>
    </div>

    <!-- Assignee Field -->
    <div v-else-if="field.key === 'assignee'" class="w-full">
      <div v-if="record.assignee" class="flex items-center space-x-2">
        <Avatar class="w-6 h-6 text-xs">
          <AvatarFallback :class="getAvatarColor(record.assignee.name)">
            {{ getInitials(record.assignee.name) }}
          </AvatarFallback>
        </Avatar>
        <span class="text-gray-900">{{ record.assignee.name }}</span>
      </div>
      <Select v-else-if="editable" @update:model-value="updateAssignee">
        <SelectTrigger class="h-7 text-sm border-none bg-transparent hover:bg-gray-50">
          <SelectValue placeholder="–" />
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
      <span v-else class="text-gray-400">–</span>
    </div>

    <!-- Status Field -->
    <div v-else-if="field.key === 'status'" class="w-full">
      <span 
        v-if="record.status" 
        :class="getStatusBadgeClass(record.status)"
        class="px-2 py-1 rounded-full text-xs font-medium"
      >
        {{ record.status }}
      </span>
      <Select v-else-if="editable" @update:model-value="updateStatus">
        <SelectTrigger class="h-7 text-sm border-none bg-transparent hover:bg-gray-50">
          <SelectValue placeholder="–" />
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
      <span v-else class="text-gray-400">–</span>
    </div>

    <!-- Attachment Summary Field -->
    <div v-else-if="field.key === 'attachmentSummary'" class="w-full text-gray-400">
      <span class="flex items-center space-x-1">
        <span>Required field(s) are...</span>
        <span class="material-icons text-gray-400 text-sm">info</span>
      </span>
    </div>

    <!-- Attachments Field -->
    <div v-else-if="field.key === 'attachments'" class="w-full">
      <div v-if="record.attachments" class="flex items-center">
        <div class="w-8 h-6 bg-gray-800 rounded border"></div>
      </div>
      <Button 
        v-else-if="editable" 
        variant="ghost" 
        size="sm" 
        class="h-6 px-2 text-gray-400 hover:text-gray-600"
        @click="addAttachment"
      >
        <span class="material-icons text-sm mr-1">attach_file</span>
        Add
      </Button>
      <span v-else class="text-gray-400">–</span>
    </div>

    <!-- User Field -->
    <div v-else-if="field.key === 'user'" class="w-full">
      <input 
        v-if="isEditing || record.user" 
        v-model="localUserValue"
        class="w-full bg-transparent border-none outline-none text-gray-900 placeholder-gray-400"
        :placeholder="record.user ? '' : '–'"
        :readonly="!editable"
        @blur="saveUserChanges"
        @keydown.enter="saveUserChanges"
        @focus="isEditing = true"
      />
      <span 
        v-else 
        class="text-gray-400 cursor-pointer" 
        @click="startEditing"
      >
        –
      </span>
    </div>

    <!-- Default Field -->
    <div v-else class="w-full">
      <span class="text-gray-400">–</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import { Avatar, AvatarFallback } from './ui/avatar'
import { Button } from './ui/button'
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from './ui/select'
import type { AcceptableValue } from 'reka-ui'

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
  record: Record
  field: Field
  editable?: boolean
  searchQuery?: string
}>()

const emit = defineEmits<{
  update: [data: Partial<Record>]
}>()

// Local state
const isEditing = ref(false)
const localValue = ref(props.record.name || '')
const localUserValue = ref(props.record.user || '')

// Watch for record changes from parent
watch(() => props.record.name, (newValue) => {
  localValue.value = newValue || ''
})

watch(() => props.record.user, (newValue) => {
  localUserValue.value = newValue || ''
})

// Methods
function startEditing() {
  if (props.editable) {
    isEditing.value = true
  }
}

function saveChanges() {
  if (localValue.value !== props.record.name) {
    emit('update', { name: localValue.value })
  }
  isEditing.value = false
}

function saveUserChanges() {
  if (localUserValue.value !== props.record.user) {
    emit('update', { user: localUserValue.value })
  }
  isEditing.value = false
}

function updateAssignee(value: AcceptableValue) {
  if (typeof value === 'string') {
    const assigneeMap: { [key: string]: { name: string } } = {
      'shoaib-rehman': { name: 'Shoaib Rehman' },
      'john-doe': { name: 'John Doe' },
      'jane-smith': { name: 'Jane Smith' }
    }
    
    emit('update', { assignee: assigneeMap[value] })
  }
}

function updateStatus(value: AcceptableValue) {
  if (typeof value === 'string') {
    emit('update', { status: value })
  }
}

function addAttachment() {
  emit('update', { attachments: true })
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
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 