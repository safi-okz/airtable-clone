<script lang="ts" setup>
import { ref } from 'vue'
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
  DialogFooter,
} from './ui/dialog'
import { Button } from './ui/button'
import { ScrollArea } from './ui/scroll-area'

const layouts = [
  { id: 'list', icon: 'list', name: 'List', description: 'Organize and prioritize.' },
  { id: 'gallery', icon: 'grid_view', name: 'Gallery', description: 'Visualize image-heavy records.' },
  { id: 'kanban', icon: 'view_kanban', name: 'Kanban', description: 'Organize records into columns.' },
  { id: 'calendar', icon: 'calendar_today', name: 'Calendar', description: 'Show events by month, week, or day.' },
  { id: 'timeline', icon: 'timeline', name: 'Timeline', description: 'Visualize data across time.', pro: 'Team' },
  { id: 'form', icon: 'feed', name: 'Form', description: 'Create new records.' },
  { id: 'dashboard', icon: 'bar_chart', name: 'Dashboard', description: 'High-level view of your data using summaries & charts.' },
  { id: 'overview', icon: 'info', name: 'Overview', description: 'Highlight important pages and resources' },
  { id: 'record_review', icon: 'rate_review', name: 'Record review', description: 'Review and edit details.' },
]

const selectedLayout = ref('list')
const isMobile = ref(false)

defineProps<{ open: boolean }>()
defineEmits(['update:open', 'next'])
</script>

<template>
  <Dialog :open="open" @update:open="$emit('update:open', $event)">
    <DialogContent class="w-[1200px] h-[90vh] p-0 flex flex-col gap-0">
      <DialogHeader class="p-6 border-b">
        <DialogTitle class="text-3xl font-bold text-gray-900">Choose a layout</DialogTitle>
        <DialogDescription class="text-base text-gray-600">
          Layouts are an easy way to get started by guiding your design and workflow. Layouts are chosen once at the start of your workflow.
        </DialogDescription>
      </DialogHeader>

      <div class="flex-1 flex overflow-hidden">
        <!-- Left: Layout List -->
        <ScrollArea class="w-1/3 border-r">
          <div class="p-2 space-y-1">
            <Button
              v-for="layout in layouts"
              :key="layout.id"
              @click="selectedLayout = layout.id"
              variant="ghost"
              :class="[
                'h-auto text-left justify-start items-start p-3 w-full rounded-lg transition-all duration-200', 
                selectedLayout === layout.id 
                  ? 'bg-blue-100 text-blue-900 shadow-sm border border-blue-200' 
                  : 'hover:bg-gray-100'
              ]"
            >
              <span class="material-icons-outlined text-xl text-gray-500 mr-4 mt-1">{{ layout.icon }}</span>
              <div class="flex flex-col">
                <div class="font-semibold text-base text-gray-800 flex items-center">
                  {{ layout.name }}
                  <span v-if="layout.pro" class="ml-2 text-xs bg-blue-200 text-blue-800 font-bold px-1.5 py-0.5 rounded">{{ layout.pro }}</span>
                </div>
                <p class="text-sm text-gray-600 font-normal">{{ layout.description }}</p>
              </div>
            </Button>
          </div>
        </ScrollArea>

        <!-- Right: Preview -->
        <div class="w-2/3 bg-gray-50 flex flex-col items-center justify-center p-8 transition-all duration-300">
           <div class="w-full h-full bg-white border rounded-xl shadow-lg flex items-center justify-center p-4">
             <img src="/src/assets/layout-preview.png" alt="Layout preview" :class="['max-w-full max-h-full object-contain rounded-md transition-all duration-300', isMobile ? 'w-[300px]' : 'w-full']" />
          </div>
          <div class="flex items-center space-x-1 mt-6 bg-gray-200 p-1 rounded-lg">
            <Button @click="isMobile = false" :variant="!isMobile ? 'secondary' : 'ghost'" size="sm" class="space-x-2 rounded-md !h-9 px-4">
              <span class="material-icons-outlined text-lg">desktop_windows</span>
              <span>Desktop</span>
            </Button>
            <Button @click="isMobile = true" :variant="isMobile ? 'secondary' : 'ghost'" size="sm" class="space-x-2 rounded-md !h-9 px-4">
              <span class="material-icons-outlined text-lg">smartphone</span>
              <span>Mobile</span>
            </Button>
          </div>
        </div>
      </div>
      
      <DialogFooter class="p-4 border-t bg-white gap-2">
        <Button variant="ghost" class="h-10 px-5" @click="$emit('update:open', false)">Cancel</Button>
        <Button @click="$emit('next')" class="bg-gray-900 text-white hover:bg-black h-10 px-5">Next</Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template> 