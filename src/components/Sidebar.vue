<script lang="ts" setup>
import { Button } from './ui/button'
</script>

<template>
  <div class="w-64 bg-gray-50 border-r border-gray-200 p-3 space-y-5">
    <div>
      <h3 class="px-3 text-xs text-gray-500 font-semibold uppercase tracking-wider mb-2">Tables</h3>
      <Button variant="ghost" class="w-full justify-start h-9 px-3 bg-airtable-blue-selected text-airtable-blue-text font-semibold hover:bg-blue-100 transition-colors">
        <span class="material-icons-outlined text-base mr-2">table_rows</span>
        Students
      </Button>
      <Button variant="ghost" class="w-full justify-start h-9 px-3 text-gray-700 hover:bg-gray-100 transition-colors">
        <span class="material-icons-outlined text-base mr-2">table_rows</span>
        Teachers
      </Button>
       <Button variant="ghost" class="w-full justify-start h-9 px-3 text-gray-700 hover:bg-gray-100 transition-colors">
        <span class="material-icons-outlined text-base mr-2">table_rows</span>
        Classes
      </Button>
    </div>
    <div>
      <h3 class="px-3 text-xs text-gray-500 font-semibold uppercase tracking-wider mb-2">Interface Groups</h3>
      <Button variant="ghost" class="w-full justify-start h-9 px-3 text-gray-700 hover:bg-gray-100 transition-colors">
        <span class="material-icons-outlined text-base mr-2">folder</span>
        Group 1
      </Button>
    </div>
    <div class="pt-2 border-t border-gray-200">
      <Button variant="ghost" class="w-full justify-start h-9 px-3 text-gray-500 font-normal hover:bg-gray-100 transition-colors">
        <span class="material-icons text-lg mr-2">add</span>
        Add or import
      </Button>
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 