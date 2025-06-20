<template>
  <div class="flex flex-col h-screen w-screen bg-gray-100">
    <!-- Top Navigation Bar -->
    <TopNav v-model="activeTab" />
    <!-- Conditional Secondary Toolbar or Interfaces Nav -->
    <Toolbar v-if="activeTab !== 'Interfaces'" />
    <InterfacesNav v-else />
    <div class="flex flex-1 overflow-hidden">
      <!-- Conditional Sidebar -->
      <Sidebar v-if="activeTab !== 'Interfaces'" />
      <InterfacesSidebar v-else @open-name-dialog="openNameDialog" />
      <!-- Main Content -->
      <div class="flex-1 flex flex-col overflow-hidden">
        <DataGrid v-if="activeTab !== 'Interfaces'" class="flex-1" />
        <InterfacesMain v-else class="flex-1" />
        <BottomBar v-if="activeTab !== 'Interfaces'" />
      </div>
    </div>
    <!-- Name Interface Popup -->
    <NameInterfaceDialog :open="isNameInterfaceDialogOpen" @update:open="isNameInterfaceDialogOpen = $event" @next="openChooseLayoutDialog" />
    <ChooseLayoutDialog :open="isChooseLayoutDialogOpen" @update:open="isChooseLayoutDialogOpen = $event" @next="openConnectToTableDialog" />
    <ConnectToTableDialog :open="isConnectToTableDialogOpen" @update:open="isConnectToTableDialogOpen = $event" @back="backToChooseLayoutDialog" />
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import TopNav from './components/TopNav.vue'
import Toolbar from './components/Toolbar.vue'
import Sidebar from './components/Sidebar.vue'
import DataGrid from './components/DataGrid.vue'
import BottomBar from './components/BottomBar.vue'
import InterfacesNav from './components/InterfacesNav.vue'
import InterfacesSidebar from './components/InterfacesSidebar.vue'
import InterfacesMain from './components/InterfacesMain.vue'
import NameInterfaceDialog from './components/NameInterfaceDialog.vue'
import ChooseLayoutDialog from './components/ChooseLayoutDialog.vue'
import ConnectToTableDialog from './components/ConnectToTableDialog.vue'

const activeTab = ref('Interfaces')
const isNameInterfaceDialogOpen = ref(false)
const isChooseLayoutDialogOpen = ref(false)
const isConnectToTableDialogOpen = ref(false)

function openNameDialog() {
  isNameInterfaceDialogOpen.value = true
}

function openChooseLayoutDialog() {
  isNameInterfaceDialogOpen.value = false
  isChooseLayoutDialogOpen.value = true
}

function openConnectToTableDialog() {
  isChooseLayoutDialogOpen.value = false
  isConnectToTableDialogOpen.value = true
}

function backToChooseLayoutDialog() {
  isConnectToTableDialogOpen.value = false
  isChooseLayoutDialogOpen.value = true
}
</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
