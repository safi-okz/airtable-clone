<template>
  <aside class="w-72 bg-gray-50 border-r border-gray-200 flex flex-col h-full">
    <div class="p-4 border-b border-gray-200 flex-1">
      <div class="font-bold text-lg text-gray-800 mb-4">Untitled Base</div>
      <div class="flex items-center justify-between mb-2">
        <span class="text-xs font-semibold text-gray-500 uppercase tracking-wider">Interfaces</span>
        <DropdownMenu>
          <DropdownMenuTrigger as-child>
            <Button variant="ghost" size="icon" class="w-7 h-7 hover:bg-gray-200">
              <span class="material-icons text-base text-gray-600">add</span>
            </Button>
          </DropdownMenuTrigger>
          <DropdownMenuContent align="start">
            <DropdownMenuItem>
              <span class="material-icons-outlined text-base mr-2">wysiwyg</span> Add page
            </DropdownMenuItem>
            <DropdownMenuItem @click="$emit('open-name-dialog')">
              <span class="material-icons-outlined text-base mr-2">add_box</span> Add interface
            </DropdownMenuItem>
          </DropdownMenuContent>
        </DropdownMenu>
      </div>
      <div class="flex flex-col space-y-1">
        <Button variant="ghost" class="h-8 justify-start px-2 text-gray-700">
          <span class="material-icons-outlined text-base mr-2">folder_open</span>
          Interface
          <span class="ml-auto text-xs bg-gray-200 text-gray-600 rounded px-1.5 py-0.5">Unpublished</span>
        </Button>
        <Button variant="ghost" class="h-8 justify-start px-2 text-gray-700">
          <span class="material-icons-outlined text-base mr-2">folder_open</span>
          Student record
          <span class="ml-auto text-xs bg-gray-200 text-gray-600 rounded px-1.5 py-0.5">Unpublished</span>
        </Button>
        <Button variant="secondary" class="h-8 justify-start px-2 ml-4 bg-blue-100 text-blue-700">
          <span class="material-icons text-base mr-2">grid_on</span>
          Table 1
        </Button>
      </div>
    </div>
    <div class="p-3 text-xs text-gray-500 border-t border-gray-200 flex items-center space-x-2">
      <span class="material-icons-outlined text-base align-middle">feedback</span>
      <span>Share Interface Designer feedback</span>
    </div>
  </aside>
</template>

<script lang="ts" setup>
import { Button } from './ui/button'
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from './ui/dropdown-menu'

defineEmits(['open-name-dialog'])
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
.sidebar-item {
  font-size: 15px;
  color: #222;
  border: none;
  box-shadow: none;
}
.sidebar-item.selected:not(.table) {
  border-left: 3px solid #2563eb;
}
.sidebar-item.table.selected {
  border-left: 3px solid #2563eb;
}
.dot {
  width: 8px;
  height: 8px;
  background: #2563eb;
  border-radius: 50%;
}
</style> 