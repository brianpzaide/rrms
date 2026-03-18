<template>
  <div class="dialog">
    <div class="dialog-header">
      <strong>Bulk Edit</strong>
      <label class="switch">
        <input type="checkbox" v-model="bulkMode">
        <span class="slider"></span>
      </label>
    </div>
        '
    <div class="dialog-body">
      <div v-if="bulkMode" class="bulk-upload">
        <input type="file" @change="handleFileSelect">
        <button class="primary" @click="uploadCSV" :disabled="!selectedFile">
          Upload
        </button>
      </div>

      <div v-else class="form">
        <div class="form-group">
          <label>Category</label>
          <input v-model="newSeat.category" placeholder="Enter category">
        </div>

        <div class="form-group">
          <label>Status</label>
          <select v-model="newSeat.status">
            <option>Vacant</option>
            <option>Occupied</option>
          </select>
        </div>

        <div class="form-group">
          <label>ID</label>
          <input v-model="newSeat.id" placeholder="Enter ID">
        </div>

        <button class="primary" @click="addSeat">Save</button>
      </div>
    </div>

    <div class="dialog-footer">
      <button class="secondary" @click="closeDialogs">Close</button>
    </div>
  </div>
</template>

<script setup>
  import { ref } from "vue"

  const emit = defineEmits([
    "add-seat",
    "bulk-add-seats",
    "close-dialog"
  ])


  const newSeat = ref({
    id: "",
    category: "",
    status: "Vacant"
  })

  const bulkMode = ref(false)

  const selectedFile = ref(null)

  function handleFileSelect(event) {
    const file = event.target.files[0]
    selectedFile.value = file || null
  }

  function uploadCSV() {
    if (!selectedFile.value) return
    const reader = new FileReader()
    reader.onload = () => {
      emit("bulk-add-seats", reader.result)
      console.log(reader.result)
      selectedFile.value = null
    }
    reader.readAsText(selectedFile.value)
  }

  function addSeat() {
    emit("add-seat", newSeat.value)
    newSeat.value = { id: "", category: "", status: "Vacant" }
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

  .switch {
    position: relative;
    display: inline-block;
    width: 40px;
    height: 22px;
  }

  .switch input {
    opacity: 0;
    width: 0;
    height: 0;
  }

  .slider {
    position: absolute;
    cursor: pointer;
    inset: 0;
    background-color: #ccc;
    border-radius: 999px;
    transition: 0.3s;
  }

  .slider::before {
    content: "";
    position: absolute;
    height: 16px;
    width: 16px;
    left: 3px;
    top: 3px;
    background: white;
    border-radius: 50%;
    transition: 0.3s;
  }

  .switch input:checked + .slider {
    background-color: #4f46e5;
  }

  .switch input:checked + .slider::before {
    transform: translateX(18px);
  }

  .bulk-upload {
    display: flex;
    gap: 8px;
    align-items: center;
  }

  .bulk-upload input[type="file"] {
    flex: 1;
  }
</style>