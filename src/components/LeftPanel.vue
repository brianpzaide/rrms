<template>
    <div class="l1">
      <SeatsPanel
        :seats="seats"
        @refresh-seats="fetchSeats"
        @add-seat="addSeat"
        @bulk-add-seats="bulkAddSeats"
        @delete-seats="deleteSeats"
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

          <div class="refresh-icon" @click="fetchSeats">⟳</div>
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
                v-for="patron in patrons"
                :key="patron.id"
                :class="{ 'selected' : selectedPatron === patron.id }"
                @click="togglePatronSelection(patron.id)"
                >
                  <td>{{ patron.seat }}</td>
                  <td>{{ patron.name + " " + patron.name + " " + patron.name }}</td>
                  <td>
                    <span :class="['status-pill', {'paid': patron.payment_status, 'pending': !patron.payment_status}]">
                    {{patron.payment_status? 'paid': 'pending'}}
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

    const seatCategories = ["AC", "Non AC"]

    const seats = ref(null)
    const patrons = ref(null)
    const selectedPatron = ref(null)
    const showPendingOnly = ref(false)

    function generateSeats() {
      const rrms_seats = new Array()
      const rows = ["A", "B"];
      for (let r of rows) {
        for (let i = 1; i <= 20; i++) {
          const k = Math.floor(Math.random()*3)
          rrms_seats.push({
            id: i+r,
            is_vacant: k == 0 ? true : false,
            category: seatCategories[Math.floor(Math.random()*2)]
          })
        }
      }
      seats.value = rrms_seats 
    }

    function refreshSeats(event) {
      alert("Seats refreshed!");
    }

    function generatePatrons() {
      const category_amount = [{category: "tier 1", amount: "1000"}, {category: "tier 2", amount: "2000"}, {category: "tier 3", amount: "3000"}]
      const rrms_patrons = new Array()
      for (let i = 1; i <= 20; i++) {
        const n = generateName()
        const tier = Math.floor(Math.random()*2)
        rrms_patrons.push({
          id: i,
          name: n,
          phone: generatePhone(),
          email: n + "gmail.com",
          start_date: "Mon, Feb 02, 2026",
          category: category_amount[tier].category,
          amount:category_amount[tier].amount,
          payment_status: Math.floor(Math.random()*3) === 0 ? true : false,
          seat: i,
        })
      }
      patrons.value = rrms_patrons 
    }

    function generateName() {
      const alphabets = "abcdefghijklmnopqrstuvwxyz"
      const l = 4 + Math.floor(Math.random()*5)
      let sofar = ""
      for (let i = 0; i <= l; i++){
        sofar +=  alphabets[Math.floor(Math.random()*25)]
      }
      return sofar
    }

    function generatePhone(){
      const nums = "1234567890"
      let sofar = ""
      for (let i = 1; i <= 10; i++){
        let k = nums[Math.floor(Math.random()*9)]
        sofar += k
      }
      return sofar
    }


    // Initial load
    generateSeats();
    generatePatrons();


    function fetchSeats(){
      console.log("fetching seats")
    }
    function addSeat(s){
      console.log("add seat")
    }
    function bulkAddSeats(f){
      console.log("bulk adding seats")
    }
    function deleteSeats(s){
      console.log("bulk deleting seats")
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