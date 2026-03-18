<template>
    <div class="tab-content">
      <h3>Add New Patron</h3>

      <div class="form-group">
        <label>Full Name</label>
        <input type="text">
      </div>

      <div class="form-group">
        <label>Phone</label>
        <input type="text">
      </div>

      <div class="form-group">
        <label>Seat</label>
        <input type="text">
      </div>

      <div class="form-group">
        <label>Email</label>
        <input type="email">
      </div>

      <div class="form-group">
        <label>Category</label>
        <input type="text">
      </div>

      <div class="form-group">
        <label>Amount</label>
        <input type="number">
      </div>

      <div class="button-wrapper">
        <button @click="addNewPatron">
          Add Patron
        </button>
      </div>
    </div>
</template>

<script setup>
  import { ref } from "vue"

  const name = ref("")
  const phone = ref("")
  const email = ref("")
  const seat = ref("")
  const category = ref("")
  const amount = ref(0)

  const emit = defineEmits([
    "new-contract"
  ])

  function addNewPatron(){
    if ( name.value === "" || 
         phone.value === "" || 
         email.value === "" || 
         seat.value === "" || 
         category.value === "" || 
         amount.value === 0) {
      return
    }
    emit("new-contract", 
      name.value,
      phone.value,
      email.value,
      seat.value,
      category.value,
      amount.value
    )
  }

</script>


<style scoped>
  .tab-content {
    flex: 1;
    overflow: auto;
    gap: 15px;
  }
  
  .form-group {
    display: flex;
    flex-direction: column;
    gap: 5px;
    margin-top: 15px;
  }

  .form-group input {
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  .button-wrapper {
    display: flex;
    justify-content: center;
    margin-top: 20px;
  }

  button {
    margin-top: 20px;
    padding: 8px 12px;
    border: none;
    border-radius: 9px;
    background: #007bff;
    color: white;
    cursor: pointer;
    width: 75%;
  }
</style>