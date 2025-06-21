<template>
  <Dialog v-model:open="isOpen">
    <DialogContent class="max-w-md">
      <DialogHeader>
        <DialogTitle>Add Field</DialogTitle>
        <DialogDescription>
          Create a new field for your table
        </DialogDescription>
      </DialogHeader>
      
      <div class="space-y-4">
        <!-- Field Name -->
        <div>
          <label class="text-sm font-medium text-gray-700 block mb-2">Field name</label>
          <Input 
            v-model="fieldName"
            placeholder="Enter field name..."
            class="w-full"
            @keydown.enter="createField"
          />
        </div>

        <!-- Field Type -->
        <div>
          <label class="text-sm font-medium text-gray-700 block mb-2">Field type</label>
          <Select v-model="fieldType">
            <SelectTrigger class="w-full">
              <SelectValue />
            </SelectTrigger>
            <SelectContent>
              <SelectItem value="text">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">text_fields</span>
                  <div>
                    <div class="font-medium">Single line text</div>
                    <div class="text-xs text-gray-500">Text with a character limit</div>
                  </div>
                </div>
              </SelectItem>
              <SelectItem value="number">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">123</span>
                  <div>
                    <div class="font-medium">Number</div>
                    <div class="text-xs text-gray-500">Integer or decimal numbers</div>
                  </div>
                </div>
              </SelectItem>
              <SelectItem value="select">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">radio_button_checked</span>
                  <div>
                    <div class="font-medium">Single select</div>
                    <div class="text-xs text-gray-500">Pick one option from a list</div>
                  </div>
                </div>
              </SelectItem>
              <SelectItem value="multiselect">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">checklist</span>
                  <div>
                    <div class="font-medium">Multiple select</div>
                    <div class="text-xs text-gray-500">Pick multiple options from a list</div>
                  </div>
                </div>
              </SelectItem>
              <SelectItem value="user">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">person</span>
                  <div>
                    <div class="font-medium">Collaborator</div>
                    <div class="text-xs text-gray-500">Assign to someone</div>
                  </div>
                </div>
              </SelectItem>
              <SelectItem value="date">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">calendar_today</span>
                  <div>
                    <div class="font-medium">Date</div>
                    <div class="text-xs text-gray-500">Date with time</div>
                  </div>
                </div>
              </SelectItem>
              <SelectItem value="attachment">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">attachment</span>
                  <div>
                    <div class="font-medium">Attachment</div>
                    <div class="text-xs text-gray-500">Files and images</div>
                  </div>
                </div>
              </SelectItem>
              <SelectItem value="checkbox">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">check_box</span>
                  <div>
                    <div class="font-medium">Checkbox</div>
                    <div class="text-xs text-gray-500">Check or uncheck</div>
                  </div>
                </div>
              </SelectItem>
              <SelectItem value="url">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">link</span>
                  <div>
                    <div class="font-medium">URL</div>
                    <div class="text-xs text-gray-500">Valid web address</div>
                  </div>
                </div>
              </SelectItem>
              <SelectItem value="email">
                <div class="flex items-center space-x-2">
                  <span class="material-icons text-gray-500 text-sm">email</span>
                  <div>
                    <div class="font-medium">Email</div>
                    <div class="text-xs text-gray-500">Valid email address</div>
                  </div>
                </div>
              </SelectItem>
            </SelectContent>
          </Select>
        </div>

        <!-- Options for Select Fields -->
        <div v-if="fieldType === 'select' || fieldType === 'multiselect'" class="space-y-2">
          <label class="text-sm font-medium text-gray-700 block">Options</label>
          <div v-for="(option, index) in fieldOptions" :key="index" class="flex items-center space-x-2">
            <Input 
              v-model="option.value"
              :placeholder="`Option ${index + 1}`"
              class="flex-1"
            />
            <Button 
              variant="ghost" 
              size="icon" 
              class="h-8 w-8"
              @click="removeOption(index)"
            >
              <span class="material-icons text-sm text-gray-400">close</span>
            </Button>
          </div>
          <Button 
            variant="ghost" 
            size="sm" 
            class="w-full border border-dashed border-gray-300 hover:border-gray-400"
            @click="addOption"
          >
            <span class="material-icons text-sm mr-1">add</span>
            Add option
          </Button>
        </div>

        <!-- Field Description -->
        <div>
          <label class="text-sm font-medium text-gray-700 block mb-2">Description (optional)</label>
          <Input 
            v-model="fieldDescription"
            placeholder="Add a description for this field..."
            class="w-full"
          />
        </div>
      </div>

      <DialogFooter>
        <Button variant="outline" @click="cancel">Cancel</Button>
        <Button @click="createField" :disabled="!fieldName.trim()">
          Create field
        </Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>

<script setup lang="ts">
import { ref, watch, computed } from 'vue'
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
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from './ui/select'

interface FieldOption {
  value: string
  color?: string
}

interface NewField {
  key: string
  label: string
  type: string
  icon: string
  description?: string
  options?: FieldOption[]
}

const props = defineProps<{
  open: boolean
}>()

const emit = defineEmits<{
  'update:open': [value: boolean]
  'create-field': [field: NewField]
}>()

// Local state
const fieldName = ref('')
const fieldType = ref('text')
const fieldDescription = ref('')
const fieldOptions = ref<FieldOption[]>([{ value: '' }])

const isOpen = computed({
  get: () => props.open,
  set: (value: boolean) => emit('update:open', value)
})

// Watch for when dialog closes to reset form
watch(() => props.open, (newValue) => {
  if (!newValue) {
    resetForm()
  }
})

// Methods
function resetForm() {
  fieldName.value = ''
  fieldType.value = 'text'
  fieldDescription.value = ''
  fieldOptions.value = [{ value: '' }]
}

function addOption() {
  fieldOptions.value.push({ value: '' })
}

function removeOption(index: number) {
  if (fieldOptions.value.length > 1) {
    fieldOptions.value.splice(index, 1)
  }
}

function getFieldIcon(type: string): string {
  const iconMap: Record<string, string> = {
    text: 'text_fields',
    number: '123',
    select: 'radio_button_checked',
    multiselect: 'checklist',
    user: 'person',
    date: 'calendar_today',
    attachment: 'attachment',
    checkbox: 'check_box',
    url: 'link',
    email: 'email'
  }
  return iconMap[type] || 'text_fields'
}

function createField() {
  if (!fieldName.value.trim()) return

  const newField: NewField = {
    key: fieldName.value.toLowerCase().replace(/\s+/g, '_'),
    label: fieldName.value,
    type: fieldType.value,
    icon: getFieldIcon(fieldType.value),
    description: fieldDescription.value || undefined
  }

  // Add options for select fields
  if (fieldType.value === 'select' || fieldType.value === 'multiselect') {
    newField.options = fieldOptions.value.filter(opt => opt.value.trim())
  }

  console.log('Creating field:', newField)
  emit('create-field', newField)
  emit('update:open', false)
}

function cancel() {
  emit('update:open', false)
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 