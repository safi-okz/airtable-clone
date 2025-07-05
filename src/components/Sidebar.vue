<template>
  <aside class="w-64 bg-white border-r border-gray-200 flex flex-col h-full">
    <div class="p-4 border-b border-gray-100">
      <div class="font-semibold text-gray-700 mb-2">Views</div>
      <div class="flex flex-col gap-1">
        <Button variant="secondary" class="justify-start"><span class="material-icons text-base mr-2">table_chart</span> Grid view</Button>
      </div>
    </div>
    <ScrollArea class="flex-1 p-4">
      <div class="text-xs text-gray-500 mb-2">Create...</div>
      <div class="flex flex-col gap-1">
        <Button variant="ghost" class="justify-start"><span class="material-icons text-base mr-2">table_chart</span> Grid</Button>
        <Button variant="ghost" class="justify-start"><span class="material-icons text-base mr-2">calendar_today</span> Calendar</Button>
        <Button variant="ghost" class="justify-start"><span class="material-icons text-base mr-2">photo_library</span> Gallery</Button>
        <Button variant="ghost" class="justify-start"><span class="material-icons text-base mr-2">view_kanban</span> Kanban</Button>
        <Button variant="ghost" class="justify-start"><span class="material-icons text-base mr-2">timeline</span> Timeline <span class="ml-auto text-xs bg-gray-200 rounded px-1">Team</span></Button>
        <Button variant="ghost" class="justify-start"><span class="material-icons text-base mr-2">list</span> List</Button>
        <Button variant="ghost" class="justify-start"><span class="material-icons text-base mr-2">gantt_chart</span> Gantt <span class="ml-auto text-xs bg-gray-200 rounded px-1">Team</span></Button>
        <Button variant="ghost" class="justify-start"><span class="material-icons text-base mr-2">add</span> New section <span class="ml-auto text-xs bg-gray-200 rounded px-1">Team</span></Button>
      </div>
      <div class="mt-4">
        <Button variant="ghost" class="justify-start text-purple-700 hover:text-purple-700"><span class="material-icons text-base mr-2">description</span> Form</Button>
      </div>
    </ScrollArea>
  </aside>
</template>

<script lang="ts" setup>
import { Button } from '@/components/ui/button'
import { ScrollArea } from '@/components/ui/scroll-area'
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style> 