<template>
  <div class="tab-content active">
    <div class="l2s1">
      <h3>{{selectedPatron.name}}</h3>
      <div class="detail-row">
        <span class="label">Phone:</span>
        <span class="value">{{ selectedPatron.phone }}</span>
        <span class="icon"></span>
      </div>

      <div class="detail-row">
        <span class="label">Email:</span>
        <span class="value">{{ selectedPatron.email }}</span>
        <span class="icon"></span>
      </div>

      <div class="detail-row">
        <span class="label">Joining Date:</span>
        <span class="value">{{ selectedPatron.start_date }}</span>
        <span class="icon"></span>
      </div>

      <div class="detail-row">
        <span class="label">Seat:</span>
        <span v-if="editingField !== 'seat'">
          <span class="value">{{ selectedPatron.seat }}</span>
          <span class="icon edit-icon" @click="startEdit('seat')">✏️</span>
        </span>
        <span v-else>
          <input v-model="editValues.seat" />
          <button @click="saveEdit('seat')">✔</button>
          <button @click="cancelEdit">✖</button>
        </span>
      </div>


      <div class="detail-row">
        <span class="label">Amount:</span>
        <span v-if="editingField !== 'amount'">
          <span class="value">{{ selectedPatron.amount }}</span>
          <span class="icon edit-icon" @click="startEdit('amount')">✏️</span>
        </span>
        <span v-else>
          <input v-model="editValues.amount" />
          <button @click="saveEdit('amount')">✔</button>
          <button @click="cancelEdit">✖</button>
        </span>
      </div>

      <div class="detail-row">
        <span class="label">Category:</span>
        <span v-if="editingField !== 'category'">
          <span class="value">{{ selectedPatron.category }}</span>
          <span class="icon edit-icon" @click="startEdit('category')">✏️</span>
        </span>
        <span v-else>
          <input v-model="editValues.category" />
          <button @click="saveEdit('category')">✔</button>
          <button @click="cancelEdit">✖</button>
        </span>
      </div>

      <div class="detail-row">
        <span class="label">Payment Status:</span>
        <span class="value" v-if="!selectedPatron.paid">
          <label>
            <input
              type="radio"
              value="pending"
              checked
              disabled
            />
            <span :class="['status-pill', 'pending']">Pending</span>
          </label>
          <label>
            <input
              type="radio"
              value="paid"
              @change="markCurrentMonthPaid"
            />
            <span :class="['status-pill', 'paid']">Paid</span>
          </label>
        </span>
        <span v-else :class="['value', 'status-pill', {'paid': selectedPatron.paid, 'pending': !selectedPatron.paid}]">
          Paid
        </span>
      </div>
    </div>

    <div class="l2s2">
      <table>
        <thead>
          <tr>
            <th>Date</th>
            <th>Amount</th>
            <th>Payment Status</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="payment in selectedPatron.payments"
            :key="payment.id"
          >
            <td>{{payment.due_date}}</td>
            <td>{{payment.amount}}</td>
            <!-- <td>
              <span :class="['status-pill', {'paid': payment.status, 'pending': !payment.status}]">
                {{payment.status ? 'paid': 'pending'}}
              </span>
            </td> -->

            <td>
              <span v-if="payment.status">
                <span class="status-pill paid">paid</span>
              </span>
              <span v-else>
                <label>
                  <input
                    type="radio"
                    name="payment-{{payment.id}}"
                    checked
                    disabled
                  />
                  Pending
                </label>
                <label>
                  <input
                    type="radio"
                    name="payment-{{payment.id}}"
                    @change="markPayment(payment)"
                  />
                  Paid
                </label>
              </span>
            </td>


          </tr>
        </tbody>
      </table>
    </div>

    <div class="sec2-buttons">
      <button
        @click="paymentMade"
      >
        Paid For Current Month
      </button>
      <button class="danger"
        @click="terminateContract"
      >
        Terminate Contract
      </button>
    </div>
  </div>
</template>

<script setup>
  import { ref } from "vue"

  const props = defineProps({
    selectedPatron: {
      type: Object
    },
  })

  const emit = defineEmits([
    "terminate-contract",
    "current-payment-made",
    "payment-made-for"
  ])

  // holds one of "seat" | "category" | "amount" | null at any given point of time
  const editingField = ref(null) 
  const editValues = ref({
    seat: "",
    category: "",
    amount: ""
  })

  function startEdit(field) {
    editingField.value = field
    editValues.value[field] = props.selectedPatron[field]
  }

  function cancelEdit() {
    editingField.value = null
  }

  function saveEdit(field) {
    emit("update-patron-field", {
      id: props.selectedPatron.id,
      field,
      value: editValues.value[field]
    })
    editingField.value = null
  }

  function markCurrentMonthPaid() {
    emit("current-payment-made", props.selectedPatron.id)
  }

  function markPayment(payment) {
    emit("payment-made-for", {
      patronId: props.selectedPatron.id,
      paymentId: payment.id
    })
  }

  // **************************To Remove******************************************
  function paymentMade(){
    if (!props.selectedPatron) {
      return
    }
    emit("current-payment-made", props.selectedPatron.id)
  }

  function paymentMadeFor(month, year){
    if (!props.selectedPatron) {
      return
    }
    emit("payment-made-for", props.selectedPatron.id, month, year)
  }
  // *******************************************************************************

  function terminateContract(){
    if (!props.selectedPatron) {
      return
    }
    emit("terminate-contract", props.selectedPatron.id)
  }
</script>


<style scoped>
  h3 {
    text-align: center;
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
  }

  tbody tr {
    cursor: pointer;
  }

  .tab-content.active {
    display: flex;
    flex-direction: column;
  }

  .l2s1 {
    background: #f9fafb;
    padding: 10px;
    border-radius: 4px;
    width: 100%;
  }
  


  .detail-row {
    display: flex;
    align-items: center;
  }

  .label {
    width: 160px;
  }

  .value {
    flex: 1;
  }

  .icon {
    margin-left: auto;
  }

  .edit-icon {
    cursor: pointer;
    opacity: 0.6;
  }

  .edit-icon:hover {
    opacity: 1;
  }




  .l2s2 {
    flex: 1;
    overflow: auto;
  }

  .sec2-buttons {
    display: flex;
    justify-content: space-between;
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

  button {
    padding: 8px 12px;
    border: none;
    border-radius: 4px;
    background: #007bff;
    color: white;
    cursor: pointer;
    margin-top: 10px;
  }

  button.danger { background: #dc3545; }
</style>