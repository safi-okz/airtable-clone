<script lang="ts" setup>
import { Tabs, TabsList, TabsTrigger } from './ui/tabs'
import { Button } from './ui/button'
import { Avatar, AvatarFallback } from './ui/avatar'

defineProps<{ modelValue: string }>()
defineEmits(['update:modelValue'])

const tabs = ['Data', 'Automations', 'Interfaces', 'Forms']
</script>

<template>
  <nav class="flex items-center h-12 justify-between px-4 bg-gradient-to-r from-purple-600 to-purple-700 text-white shadow-sm w-full font-sans">
    <!-- Left: Logo & Name -->
    <div class="flex items-center space-x-4">
      <div class="flex items-center space-x-2 font-medium text-sm">
        <span class="material-icons text-white text-lg">database</span>
        <span class="font-medium text-white">Untitled Base</span>
        <span class="material-icons text-white/70 text-sm">arrow_drop_down</span>
      </div>

      <!-- Tabs -->
      <Tabs :model-value="modelValue" @update:model-value="$emit('update:modelValue', $event)" class="w-[400px]">
        <TabsList class="bg-white/10 backdrop-blur-sm rounded-md p-0.5 h-8 border border-white/10">
          <TabsTrigger v-for="tab in tabs" :key="tab" :value="tab" class="rounded-sm text-sm px-3 data-[state=active]:bg-white/20 data-[state=active]:text-white text-white/80 hover:text-white transition-colors">
            {{ tab }}
          </TabsTrigger>
        </TabsList>
      </Tabs>
    </div>

    <!-- Right: Controls -->
    <div class="flex items-center space-x-3">
      <Button variant="ghost" class="text-white hover:bg-white/10 font-medium h-8 px-3 text-sm">
        <span class="material-icons text-sm mr-1">share</span>
        Share
      </Button>
      <Button variant="ghost" size="icon" class="hover:bg-white/10 w-8 h-8">
        <span class="material-icons text-lg">notifications_none</span>
      </Button>
      <Avatar class="w-8 h-8 bg-teal-400 text-white font-bold text-sm">
        <AvatarFallback class="bg-teal-400 text-white">S</AvatarFallback>
      </Avatar>
    </div>
  </nav>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style>
