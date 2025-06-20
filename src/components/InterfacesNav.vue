<template>
  <div class="flex items-center justify-between h-14 border-b border-gray-200 px-4 bg-white">
    <!-- Left Section -->
    <div class="flex items-center space-x-4">
      <Button variant="ghost" class="font-medium text-gray-700 hover:bg-gray-100 h-9 px-3">
        <span class="material-icons text-lg mr-2">menu</span>
        <span>Pages</span>
      </Button>
      <div class="flex items-center space-x-2">
        <Switch id="preview-switch" />
        <Label for="preview-switch" class="font-medium text-gray-700">Preview</Label>
      </div>
      <Button variant="ghost" class="space-x-2 text-gray-700 hover:bg-gray-100 h-9 px-3">
        <Avatar class="w-6 h-6">
          <AvatarFallback class="bg-gray-700 text-white text-xs font-bold">S</AvatarFallback>
        </Avatar>
        <span>View as Yourself</span>
        <span class="material-icons text-lg">expand_more</span>
      </Button>
    </div>

    <!-- Right Section -->
    <div class="flex items-center space-x-2">
      <span class="text-sm text-gray-500">Interface has unpublished changes</span>
      <Button variant="outline" class="h-9 px-4 border-gray-300">Share</Button>
      <Button class="h-9 px-4 bg-blue-600 text-white hover:bg-blue-700">Publish</Button>
      <Button variant="ghost" class="h-9 px-4 text-gray-700 bg-gray-100 hover:bg-gray-200">Done</Button>
      <Button variant="ghost" size="icon" class="w-8 h-8 hover:bg-gray-100">
        <span class="material-icons text-gray-600">expand_more</span>
      </Button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { Button } from './ui/button'
import { Switch } from './ui/switch'
import { Label } from './ui/label'
import { Avatar, AvatarFallback } from './ui/avatar'
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 