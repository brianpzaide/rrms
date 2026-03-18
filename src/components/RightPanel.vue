<template>
  <div class="tabs">
    <div :class="['tab', {'active': activeTab==0} ]" 
    @click="switchTab(0)">
        Patron Details
    </div>
    <div :class="['tab', {'active': activeTab==1} ]" 
    @click="switchTab(1)">
      Add Patron
    </div>
  </div>

  <!-- add the bodies for each of the tabs -->
  <PatronDetails :selectedPatron="selectedPatron" v-if="activeTab === 0 && selectedPatron" />
  <NewPatron v-if="activeTab===1"/>

</template>

<script setup>
  import PatronDetails from './PatronDetails.vue'
  import NewPatron from './NewPatron.vue'

  import { ref, computed } from "vue"

  const props = defineProps({
    selectedPatron: {
      type: Object
    },
  })
  
  
  const activeTab = ref(1)
  

  function switchTab(tabId){
    console.log("switchTab called", activeTab.value)
    activeTab.value = tabId
  }

</script>


<style scoped>

  .tabs {
    display: flex;
    border-bottom: 1px solid #ccc;
  }

  .tab {
    padding: 10px 15px;
    cursor: pointer;
    background: #f0f0f0;
    border: 1px solid #ccc;
    border-bottom: none;
    margin-right: 5px;
    border-radius: 4px 4px 0 0;
  }

  .tab.active {
    background: #fff;
    font-weight: bold;
  }
</style>