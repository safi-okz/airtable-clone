<script lang="ts" setup>
import { Button } from './ui/button'
import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from './ui/select'
</script>

<template>
  <div class="flex items-center justify-between h-11 border-b border-gray-200 px-3 bg-white shadow-sm">
    <!-- Left Controls -->
    <div class="flex items-center space-x-1">
      <Button variant="ghost" class="h-8 px-3 space-x-1 hover:bg-gray-100 transition-colors">
        <span class="material-icons text-base text-gray-700">grid_view</span>
        <span class="font-semibold text-sm text-gray-800">Grid view</span>
        <span class="material-icons text-base text-gray-500">arrow_drop_down</span>
      </Button>
      <div class="flex items-center ml-2">
        <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100 transition-colors">
          <span class="material-icons-outlined text-base text-gray-600">visibility_off</span>
        </Button>
        <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100 transition-colors">
          <span class="material-icons-outlined text-base text-gray-600">filter_list</span>
        </Button>
        <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100 transition-colors">
          <span class="material-icons-outlined text-base text-gray-600">group</span>
        </Button>
        <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100 transition-colors">
          <span class="material-icons-outlined text-base text-gray-600">sort</span>
        </Button>
         <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100 transition-colors">
          <span class="material-icons-outlined text-base text-gray-600">palette</span>
        </Button>
        <Button variant="ghost" class="h-8 px-2 hover:bg-gray-100 transition-colors">
           <span class="material-icons-outlined text-base text-gray-600">height</span>
        </Button>
      </div>
    </div>
    
    <!-- Right Controls -->
    <div class="flex items-center space-x-2">
      <Select default-value="find-in-page">
        <SelectTrigger class="h-8 w-[130px] text-sm text-gray-600 border-gray-200 focus:ring-1 focus:ring-blue-500 focus:border-blue-500">
          <div class="flex items-center">
            <span class="material-icons text-base mr-1 text-gray-500">search</span>
            <SelectValue placeholder="Find" />
          </div>
        </SelectTrigger>
        <SelectContent>
          <SelectGroup>
            <SelectItem value="find-in-page">Find in page</SelectItem>
          </SelectGroup>
        </SelectContent>
      </Select>
      <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100 transition-colors">
        <span class="material-icons-outlined text-base text-gray-600">history</span>
      </Button>
      <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100 transition-colors">
        <span class="material-icons-outlined text-base text-gray-600">share</span>
      </Button>
      <Button variant="ghost" size="icon" class="h-8 w-8 hover:bg-gray-100 transition-colors">
        <span class="material-icons text-base text-gray-600">fullscreen</span>
      </Button>
      <Button variant="ghost" class="h-8 px-3 space-x-1 hover:bg-gray-100 transition-colors">
        <span class="font-semibold text-sm text-gray-800">Automate</span>
        <span class="material-icons text-base text-gray-500">arrow_drop_down</span>
      </Button>
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 