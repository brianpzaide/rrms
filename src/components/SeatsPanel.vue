<template>
  <div class="l1s1">
    <div class="l1s1-header seats-header">
      <div class="seats-left">
        <h2>Seats</h2>
        <div class="refresh-icon" @click="refreshSeats()">⟳</div>
      </div>

      <div class="seats-right">
        <select v-model="selectedCategory" class="category-dropdown secondary">
          <option value="">All</option>
          <option
            v-for="category in categories"
            :key="category"
            :value="category">
            {{ category }}
          </option>
        </select>
        <div class="menu-wrapper">
          <span class="dots-menu" @click="toggleMenu">
            ⋮
          </span>
          <div v-if="showMenu" class="menu">
            <div @click="openAddDialog">Add Seats</div>
            <div @click="openDeleteDialog">Delete Seats</div>
          </div>
        </div>
      </div>
    </div>
    <div class="seat-scroll">
      <div
        v-for="seat in filteredSeats"
        :key="seat.id"
        :class="['seat', {'vacant': seat.is_vacant, 'occupied': !seat.is_vacant}, { selected: selectedSeat === seat.id }]"
        @click="toggleSeatSelection(seat.id)"
      >
        {{ seat.id }}
      </div>
    </div>

    <div v-if="showAddDialog" class="dialog-overlay">
      <AddSeatDialog 
        @close-dialog="closeDialogs"
        @add-seat="addSeat"
        @bulk-add-seats="bulkAddSeats"
      />
    </div>

    <div v-if="showDeleteDialog" class="dialog-overlay">
      <DeleteSeatDialog 
      :categories="categories"
      :seats="seats"
      @close-dialog="closeDialogs"
      @delete-seats="deleteSeats"
    />
    </div>
  </div>
</template>

<script setup>
import AddSeatDialog from './AddSeatDialog.vue'
import DeleteSeatDialog from './DeleteSeatDialog.vue'
import { ref, computed, watch } from "vue"

const props = defineProps({
  seats: {
    type: Array,
    required: true
  }
})

const emit = defineEmits([
  "add-seat",
  "bulk-add-seats",
  "delete-seats",
  "refresh-seats",
  "fetch-patron-for-seat",
])

const selectedCategory = ref("")
const showMenu = ref(false)

const showAddDialog = ref(false)
const showDeleteDialog = ref(false)

const selectedSeat = ref(null)

watch(selectedSeat, (value) =>{
  // if (value){
  //   emit("fetch-patron-for-seat", value)
  //   console.log("SeatesPanel: fetch patron for seatd", value)
  // }
  emit("fetch-patron-for-seat", value)
})

function toggleSeatSelection(seatId){
  selectedSeat.value = (selectedSeat.value === null) || (selectedSeat.value !== seatId) ? seatId : null
}

function refreshSeats(){
  emit('refresh-seats')
  selectedSeat.value = null
}

function toggleMenu() {
  showMenu.value = !showMenu.value
}

function openAddDialog() {
  showAddDialog.value = true
  showMenu.value = false
}

function openDeleteDialog() {
  showDeleteDialog.value = true
  showMenu.value = false
}

function closeDialogs() {
  showAddDialog.value = false
  showDeleteDialog.value = false
}

const categories = computed(() => {
  const set = new Set(props.seats.map(s => s.category))
  return Array.from(set)
})

const filteredSeats = computed(() => {
  if (!selectedCategory.value) return props.seats
  return props.seats.filter(
    s => s.category === selectedCategory.value
  )
})

function addSeat(newSeat) {
  emit("add-seat", newSeat)
  console.log(`SeatsPanel: new seat with ID: ${newSeat.id} Category: ${newSeat.category} Status: ${newSeat.status} added`)
  closeDialogs()
}

function bulkAddSeats(fileContent){
  emit("bulk-add-seats", fileContent)
  console.log("SeatesPanel: bulk-add-seats", fileContent)
  closeDialogs()
}

function deleteSeats(seatsToDelete) {
  console.log("SeatsPanel", seatsToDelete)
  emit("delete-seats", seatsToDelete)
  for (let std of seatsToDelete){
    console.log(`SeatsPanel: seat with ID: ${std} to be deleted`)
  }
  closeDialogs()
}
</script>

<style scoped>

  .refresh-icon {
    cursor: pointer;
    font-size: 20px;
  }

  .seat-scroll {
    display: flex;
    gap: 10px;
    overflow-x: auto;
    padding: 10px 0;
  }

  .seat {
    min-width: 50px;
    height: 40px;

    display: flex;
    align-items: center;
    justify-content: center;

    padding: 4px;
    background: #e3e7eb;
    border-radius: 4px;
    cursor: pointer;

    font-size: clamp(10px, 2vw, 14px);
    font-weight: 600;

    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .seat:hover {
    background: #cfd6dc;
  }

  .seat.vacant {
    background: #d1fae5;
    color: #065f46;
  }

  .seat.occupied {
    background: #fee2e2;
    color: #7f1d1d;
  }

  .seat.occupied:hover {
    background: #fecaca;
  }

  .seat.vacant:hover {
    background: #a7f3d0;
  }

  .selected {
    outline: 3px solid red;
  }

  .menu{
    position:absolute;
    right:0;
    background:white;
    border:1px solid #ccc;
    border-radius:4px;
  }

  .menu div{
    padding:6px 12px;
    cursor:pointer;
  }

  .menu div:hover{
    background:#eee;
  }

  .seats-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .seats-left {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .seats-right {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .category-dropdown{
    padding:6px 12px;
    border:none;
    border-radius:4px;
    color:white;
    background:#6c757d;
    cursor:pointer;

    min-width:170px;
    font-weight:500;
  }

  .category-dropdown:hover{
    background:#5a6268;
  }

  .category-dropdown:focus{
    outline:none;
  }

  .menu-wrapper {
    position: relative;
  }

  .dots-menu {
    font-size: 20px;
    cursor: pointer;
    padding: 4px 8px;
    border-radius: 4px;
  }

  .dots-menu:hover {
    background: #f1f5f9;
  }

  .menu {
    position: absolute;
    right: 0;
    top: 30px;
    background: white;
    border: 1px solid #ccc;
    border-radius: 4px;
    min-width: 140px;
    z-index: 10;
  }

  .menu div {
    padding: 6px 12px;
    cursor: pointer;
  }

  .menu div:hover {
    background: #eee;
  }

  .dialog-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.45);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 9999;
  }
</style>