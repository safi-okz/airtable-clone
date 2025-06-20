<script lang="ts" setup>
import { Tabs, TabsList, TabsTrigger } from './ui/tabs'
import { Button } from './ui/button'
import { Avatar, AvatarFallback } from './ui/avatar'

defineProps<{ modelValue: string }>()
defineEmits(['update:modelValue'])

const tabs = ['Data', 'Automations', 'Interfaces', 'Forms']
</script>

<template>
  <nav class="flex items-center h-12 justify-between px-4 bg-gradient-to-r from-airtable-orange to-orange-600 text-white gap-4 shadow-lg w-full font-sans border-b border-orange-700">
    <!-- Left: Logo & Name -->
    <div class="flex items-center space-x-4">
      <div class="flex items-center space-x-2 font-medium text-base gap-2">
        <span class="material-icons text-white/95 text-2xl drop-shadow-sm">apps</span>
        <span class="truncate max-w-[200px] font-semibold text-white/95">Student Data</span>
      </div>

      <!-- Tabs -->
      <Tabs :model-value="modelValue" @update:model-value="$emit('update:modelValue', $event)" class="w-[400px]">
        <TabsList class="bg-white/15 backdrop-blur-sm rounded-md p-0.5 h-8 border border-white/20">
          <TabsTrigger v-for="tab in tabs" :key="tab" :value="tab" class="rounded-sm text-sm px-3 data-[state=active]:bg-white/25 data-[state=active]:text-white text-white/90 hover:text-white transition-colors">
            {{ tab }}
          </TabsTrigger>
        </TabsList>
      </Tabs>
    </div>

    <!-- Right: Controls -->
    <div class="flex items-center space-x-2">
      <Button variant="ghost" class="bg-white/20 backdrop-blur-sm text-white hover:bg-white/30 font-semibold h-8 px-3 transition-all duration-200 hover:shadow-md">
        Share
      </Button>
      <Button variant="ghost" size="icon" class="hover:bg-white/20 rounded-full w-8 h-8 transition-all duration-200">
        <span class="material-icons text-xl">notifications</span>
      </Button>
      <Button variant="ghost" size="icon" class="hover:bg-white/20 rounded-full w-8 h-8 transition-all duration-200">
        <span class="material-icons text-xl">help_outline</span>
      </Button>
      <Avatar class="w-7 h-7 ring-2 ring-white/30">
        <AvatarFallback class="bg-white/30 text-xs font-bold">S</AvatarFallback>
      </Avatar>
    </div>
  </nav>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
</style>
