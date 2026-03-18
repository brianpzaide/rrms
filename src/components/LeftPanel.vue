<template>
    <div class="l1">
      <SeatsPanel
        :seats="seats"
        @refresh-seats="fetchSeats"
        @add-seat="addSeat"
        @bulk-add-seats="bulkAddSeats"
        @delete-seats="deleteSeats"
        @fetch-patron-for-seat="fetchPatronForSeat"
      />
    </div>

    <div class="l2">
      <div class="patron-toolbar">
        <div class="search-wrapper">
          <input type="text" placeholder="Search patron by name" />
          <button class="search-btn">🔍</button>
        </div>

        <div class="toolbar-right">
          <label class="checkbox-wrapper">
            <input type="checkbox" v-model="showPendingOnly">
            <span>Pending</span>
          </label>

          <div class="refresh-icon" @click="fetchPatrons">⟳</div>
        </div>
      </div>
      
      <div class="table-container">
        <table>
          <thead>
            <tr>
              <th>Seat</th>
              <th>Patron</th>
              <th>Payment Status</th>
            </tr>
          </thead>
          <tbody>
              <tr
                v-for="patron in filteredPatrons"
                :key="patron.id"
                :class="{ 'selected' : selectedPatron === patron.id }"
                @click="togglePatronSelection(patron.id)"
                >
                  <td>{{ patron.seat }}</td>
                  <td>{{ patron.name + " " + patron.name + " " + patron.name }}</td>
                  <td>
                    <span :class="['status-pill', {'paid': patron.paid, 'pending': !patron.paid}]">
                    {{patron.paid? 'paid': 'pending'}}
                    </span>
                  </td>
              </tr>
          </tbody>
        </table>
      </div>
    </div>
</template>

<script setup>
    import SeatsPanel from './SeatsPanel.vue'
    import { ref, computed, watch } from 'vue'

    const props = defineProps({
      seats: {
        type: Array,
        required: true
      },
      patrons:{
        type: Array,
        required: true
      },
      seatCategories:{
        type: Array,
        required: true
      }
    })  
    
    
    const filteredPatrons = ref(props.patrons)
    const selectedPatron = ref(null)

    const emit = defineEmits([
      "add-seat",
      "bulk-add-seats",
      "delete-seats",
      "refresh-seats",
      "refresh-patrons"
    ])

    const showPendingOnly = ref(false)
    watch(showPendingOnly, (status) => {
      if (status){
        filteredPatrons.value = props.patrons.filter(function (el) {
          return !el.paid;
        });
      }else{
        filteredPatrons.value = props.patrons
      }
    })

    const seatFilter = ref(null)
    watch(seatFilter, (seatId) => {
      if (seatId){
        filteredPatrons.value = props.patrons.filter(function (el) {
          return el.seat === seatId;
        });
      }else{
        filteredPatrons.value = props.patrons
      }
    })

    function fetchPatronForSeat(seatId){
      seatFilter.value = seatId
    }

    function fetchSeats() {
      emit("refresh-seats")
      console.log("LeftPanel: Seats refreshed!");
    }

    function fetchPatrons() {
      selectedPatron.value = null
      seatFilter.value = null
      showPendingOnly.value = false
      emit("refresh-patrons")
      console.log("LeftPanel: fetching patrons");
    }

    function addSeat(newSeat){
      emit("add-seat", newSeat)
      console.log(`LeftPanel: new seat with ID: ${newSeat.id} Category: ${newSeat.category} Status: ${newSeat.status} added`)
    }

    function bulkAddSeats(f){
      emit("bulk-add-seats", f)
      console.log("LeftPanel: bulk-add-seats", f)
      for (let ns in f){
        console.log(`LeftPanel: new seat with ID: ${ns.id} Category: ${ns.category} Status: ${ns.status} added`)
      }
    }

    function deleteSeats(seatsToDelete){
      console.log("LeftPanel", seatsToDelete)
      emit('delete-seats', seatsToDelete)
      for (let std of seatsToDelete){
        console.log(`LeftPanel: seat with ID: ${std} to be deleted`)
      }
    }

    function togglePatronSelection(id) {
      selectedPatron.value = (selectedPatron.value === null) || (selectedPatron.value !== id) ? id : null 
    }
</script>

<style scoped>
  .l1 {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-bottom: 15px;
  }

  .l2 {
    flex: 1;
    overflow: auto;
  }

  .l1s1-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .l1s2 button {
    width: 100%;
  }

  .patron-toolbar {
    display: flex;
    align-items: center;
    margin-bottom: 8px;
  }

  .search-wrapper {
    position: relative;
    flex: 0 1 320px;
  }

  .search-wrapper input {
    width: 100%;
    padding: 8px 0px 8px 8px;
    border: 1px solid #ccc;
    border-radius: 6px;
    font-size: 14px;
  }
  
  .toolbar-right {
    margin-left: auto;
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .checkbox-wrapper {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 14px;
    cursor: pointer;
    white-space: nowrap;
  }

  .checkbox-wrapper input {
    cursor: pointer;
  }

  .search-btn {
    position: absolute;
    right: 8px;
    top: 50%;
    transform: translateY(-50%);
    border: none;
    background: transparent;
    cursor: pointer;
    font-size: 16px;
    color: #666;
    padding: 0;
  }

  .search-btn:hover {
    color: #111;
  }

  .patron-toolbar button {
    white-space: nowrap;
  }

  .table-container {
    max-height: 500px;
    overflow-y: auto;
    border: 1px solid #ccc;
    margin-bottom: 20px;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    align-content: center;
  }

  th, td {
    padding: 8px;
    border: 1px solid #ccc;
    text-align: center;
    align-content: center;
  }

  th {
    background: #f0f0f0;
    position: sticky;
    top: 0;
    z-index: 1;
  }

  tbody tr {
    cursor: pointer;
  }

  tbody tr.selected {
    background-color: #dbeafe;
  }

  tbody tr:hover {
    background: #dbeafe;
  }

  .status-pill {
    text-align: center;
    font-size: 0.65rem;
    padding: 2px 8px;
    border-radius: 12px;
    color: white;
    width: 75px;
  }

  .status-pill.paid {
    background: #d1fae5;
    color: #065f46;
  }

  .status-pill.pending {
    background: #fee2e2;
    color: #7f1d1d;
  }

  .refresh-icon {
    cursor: pointer;
    font-size: 20px;
  }
</style>


