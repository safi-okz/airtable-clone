<template>
  <Dialog :open="open" @update:open="$emit('update:open', $event)">
    <DialogContent class="sm:max-w-xl p-0 gap-0">
      <DialogHeader class="p-6">
        <DialogTitle class="text-2xl font-bold text-gray-800">Name your interface</DialogTitle>
      </DialogHeader>

      <div class="px-6 pb-6">
        <p class="text-gray-600 text-base mb-5">
          Interfaces are meant to be shared, so choose a name your team can reference.
        </p>

        <!-- Icon + Input -->
        <div class="flex items-center gap-3 bg-gray-50 rounded-md p-2 mb-5 border border-gray-200 focus-within:ring-1 focus-within:ring-blue-500">
          <div class="w-9 h-9 rounded-md flex items-center justify-center shrink-0 bg-[#d83b01]">
            <span class="material-icons text-white text-xl">{{ selectedIcon }}</span>
          </div>
          <input
            type="text"
            v-model="interfaceName"
            class="w-full bg-transparent border-none outline-none text-base font-medium placeholder-gray-400"
          />
        </div>

        <!-- Icon Grid -->
        <div class="grid grid-cols-12 gap-2 max-h-40 overflow-y-auto pr-2">
          <div
            v-for="iconName in iconNames"
            :key="iconName"
            @click="selectIcon(iconName)"
            :class="[
              'w-10 h-10 flex items-center justify-center rounded-md cursor-pointer transition',
              iconName === selectedIcon
                ? 'bg-[#d83b01]/10 border border-[#d83b01] text-[#d83b01]'
                : 'text-gray-500 hover:bg-gray-100'
            ]"
          >
            <span :class="[iconName === selectedIcon ? 'material-icons' : 'material-icons-outlined', 'text-2xl']">
              {{ iconName === 'star' ? 'star_border' : iconName }}
            </span>
          </div>
        </div>
      </div>
      <DialogFooter class="px-6 py-4 bg-gray-50 sm:justify-between">
        <DialogClose as-child>
          <Button type="button" variant="ghost" class="font-semibold">
            Cancel
          </Button>
        </DialogClose>
        <Button type="button" @click="handleNext" class="bg-gray-800 hover:bg-black font-semibold">
          Next
        </Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { Button } from '@/components/ui/button';
import {
  Dialog,
  DialogContent,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogClose,
} from '@/components/ui/dialog';

defineProps<{ open: boolean }>();
const emit = defineEmits(['update:open', 'next']);

const interfaceName = ref('Interface 3');
const selectedIcon = ref('table_chart');

const iconNames = [
  'folder', 'star', 'table_chart', 'radio_button_unchecked', 'cloud', 'bar_chart', 'science', 'local_bar', 'notifications', 'bolt', 'book', 'menu_book',
  'sailing', 'apartment', 'settings', 'event', 'photo_camera', 'code', 'alt_route', 'coffee', 'replay', 'anchor', 'settings_suggest', 'chat_bubble_outline',
  'explore', 'credit_card', 'palette', 'edit', 'email', 'place', 'campaign', 'pie_chart', 'gavel', 'lightbulb', 'support'
];

function selectIcon(name: string) {
  selectedIcon.value = name;
}

function handleNext() {
  emit('next');
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/icon?family=Material+Icons');
@import url('https://fonts.googleapis.com/icon?family=Material+Icons+Outlined');

/* Custom scrollbar for webkit browsers */
.overflow-y-auto::-webkit-scrollbar {
  width: 8px;
}
.overflow-y-auto::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 10px;
}
.overflow-y-auto::-webkit-scrollbar-thumb {
  background: #ccc;
  border-radius: 10px;
}
.overflow-y-auto::-webkit-scrollbar-thumb:hover {
  background: #aaa;
}
</style> 