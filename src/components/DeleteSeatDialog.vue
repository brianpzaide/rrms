<template>
  <div class="dialog">
    <div class="form-group">
      <label>Category</label>
      <select v-model="deleteCategory">
        <option
          v-for="category in categoriesPrime"
          :key="category"
          :value="category"
        >
          {{ category }}
        </option>
      </select>
    </div>

    <div class="seat-scroll">
      <div
        v-for="seat in deleteSeatsFiltered"
        :key="seat.id"
        :class="['seat', {'vacant': seat.is_vacant, 'occupied': !seat.is_vacant}, { selected: seatsToDelete.includes(seat.id) }]"
        @click="toggleSeatSelection(seat.id)"
      >
        {{ seat.id }}
      </div>
    </div>

    <button class="danger" @click="deleteSeats">
      Delete
    </button>

    <button class="secondary" @click="closeDialogs">
      Close
    </button>
  </div>
</template>


<script setup>
  import { ref, computed } from "vue"

  const deleteCategory = ref(null)

  const props = defineProps({
    categories: {
      type: Array,
      required: true
    },
    seats:{
      type: Array,
      required: true
    }
  })

  const categoriesPrime = ref([null, ...props.categories])

  const deleteSeatsFiltered = computed(() => {
    if (!deleteCategory.value) return props.seats
    return props.seats.filter(
      s => s.category === deleteCategory.value
    )
  })


  const emit = defineEmits([
    "delete-seats",
    "close-dialog"
  ])

  const seatsToDelete = ref([])


  function toggleSeatSelection(id) {
    const idx = seatsToDelete.value.indexOf(id)

    if (idx === -1)
      seatsToDelete.value.push(id)
    else
      seatsToDelete.value.splice(idx, 1)
  }

  function deleteSeats() {
    const stds = seatsToDelete.value
    console.log("DeleteSeatsDialog", stds)
    emit("delete-seats", stds)
    seatsToDelete.value = []
  }

  function closeDialogs(){
    emit("close-dialog")
  }

</script>

<style scoped>
  .dialog {
    background: #fff;
    padding: 20px;
    border-radius: 12px;
    width: 420px;
    box-shadow: 0 10px 30px rgba(0,0,0,0.15);
    display: flex;
    flex-direction: column;
    gap: 16px;
  }

  .dialog-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .dialog-body {
    display: flex;
    flex-direction: column;
    gap: 14px;
  }

  .form-group {
    display: flex;
    flex-direction: column;
    gap: 4px;
  }

  .form-group label {
    font-size: 13px;
    color: #555;
  }

  input, select {
    padding: 8px 10px;
    border-radius: 6px;
    border: 1px solid #ccc;
    font-size: 14px;
    transition: border 0.2s, box-shadow 0.2s;
  }

  input:focus, select:focus {
    outline: none;
    border-color: #4f46e5;
    box-shadow: 0 0 0 2px rgba(79,70,229,0.15);
  }


  button {
    padding: 8px 12px;
    border-radius: 6px;
    border: none;
    cursor: pointer;
    font-size: 14px;
  }

  button.primary {
    background: #4f46e5;
    color: white;
    margin-top: 10px;
  }

  button.primary:hover {
    background: #4338ca;
  }

  button.secondary {
    background: #f3f4f6;
    color: #333;
  }

  button.secondary:hover {
    background: #e5e7eb;
  }


  .dialog-footer {
    display: flex;
    justify-content: flex-end;
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
</style>