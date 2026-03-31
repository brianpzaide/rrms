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
  <span class="label">Seat</span>

  <div class="field-content">
    <template v-if="editingField !== 'seat'">
      <span class="value">{{ selectedPatron.seat }}</span>
    </template>

    <template v-else>
      <input
        v-model="editValues.seat"
        class="field-input"
      />
    </template>
  </div>

  <div class="field-actions">
    <template v-if="editingField !== 'seat'">
      <button
        class="icon-btn"
        @click="startEdit('seat')"
      >
        ✏️
      </button>
    </template>

    <template v-else>
      <button
        class="icon-btn save"
        @click="saveEdit('seat')"
      >
        ✔
      </button>
      <button
        class="icon-btn cancel"
        @click="cancelEdit"
      >
        ✖
      </button>
    </template>
  </div>
</div>

<div class="detail-row">
  <span class="label">Amount</span>

  <div class="field-content">
    <template v-if="editingField !== 'amount'">
      <span class="value">{{ selectedPatron.amount }}</span>
    </template>

    <template v-else>
      <input
        v-model="editValues.amount"
        class="field-input"
      />
    </template>
  </div>

  <div class="field-actions">
    <template v-if="editingField !== 'amount'">
      <button
        class="icon-btn"
        @click="startEdit('amount')"
      >
        ✏️
      </button>
    </template>

    <template v-else>
      <button
        class="icon-btn save"
        @click="saveEdit('amount')"
      >
        ✔
      </button>
      <button
        class="icon-btn cancel"
        @click="cancelEdit"
      >
        ✖
      </button>
    </template>
  </div>
</div>

<div class="detail-row">
  <span class="label">Category</span>

  <div class="field-content">
    <template v-if="editingField !== 'category'">
      <span class="value">{{ selectedPatron.category }}</span>
    </template>

    <template v-else>
      <input
        v-model="editValues.category"
        class="field-input"
      />
    </template>
  </div>

  <div class="field-actions">
    <template v-if="editingField !== 'category'">
      <button
        class="icon-btn"
        @click="startEdit('category')"
      >
        ✏️
      </button>
    </template>

    <template v-else>
      <button
        class="icon-btn save"
        @click="saveEdit('category')"
      >
        ✔
      </button>
      <button
        class="icon-btn cancel"
        @click="cancelEdit"
      >
        ✖
      </button>
    </template>
  </div>
</div>

<div class="detail-row">
  <span class="label">Payment Status</span>

    <div class="field-content">
      <template v-if="!selectedPatron.paid">
        <div class="payment-status-group">
          <label class="status-option">
            <input
              type="radio"
              name="current-payment-status"
              checked
            />
            <span class="status-pill pending">pending</span>
          </label>

          <label class="status-option">
            <input
              type="radio"
              name="current-payment-status"
              @change="markCurrentMonthPaid"
            />
            <span class="status-pill paid">paid</span>
          </label>
        </div>
      </template>

      <template v-else>
        <span class="status-pill paid">paid</span>
      </template>
    </div>
  </div>

</div>
<div class="l2s2">
  <div class="payment-table-scroll">
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

            <td>
              <span v-if="payment.status">
                <span class="status-pill paid">paid</span>
              </span>
              <span v-else>
                <label>
                  <input
                    type="radio"
                    :name="`payment-${payment.id}`"
                    checked
                  />
                  <span class="status-pill pending">pending</span>
                </label>
                <label>
                  <input
                    type="radio"
                    :name="`payment-${payment.id}`"
                    @change="markPayment(payment)"
                  />
                  <span class="status-pill paid">paid</span>
                </label>
              </span>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
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
.tab-content.active {
  display: flex;
  flex-direction: column;
  gap: 20px;
  width: 100%;
}

.l2s1 {
  background: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  padding: 14px 16px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
}


h3 {
  margin: 0 0 20px;
  text-align: center;
  font-size: 1.05rem;
  font-weight: 600;
  color: #111827;
}

.detail-row {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 3px 0;
  border-bottom: 1px solid #f3f4f6;
}

.detail-row:last-child {
  border-bottom: none;
}

.label {
  width: 140px;
  flex-shrink: 0;
  font-size: 0.9rem;
  font-weight: 600;
  color: #4b5563;
}

.field-content {
  flex: 1;
  min-width: 0;
  display: flex;
  align-items: center;
}

.value {
  color: #111827;
  font-size: 0.95rem;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.field-actions {
  margin-left: auto;
  display: flex;
  align-items: center;
  gap: 6px;
  flex-shrink: 0;
}

.icon-btn {
  width: 32px;
  height: 32px;
  border: none;
  border-radius: 8px;
  background: #f3f4f6;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  padding: 0;
  margin: 0;
  transition: background-color 0.15s ease;
}

.icon-btn:hover {
  background: #e5e7eb;
}

.icon-btn.save {
  background: #dcfce7;
}

.icon-btn.save:hover {
  background: #bbf7d0;
}

.icon-btn.cancel {
  background: #fee2e2;
}

.icon-btn.cancel:hover {
  background: #fecaca;
}

.field-input {
  width: 100%;
  max-width: 240px;
  padding: 8px 10px;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  background: #ffffff;
  font-size: 0.95rem;
  color: #111827;
}

.field-input:focus {
  outline: none;
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.15);
}

.l2s2 {
  background: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
  max-height: 260px;
    display: flex;
  flex-direction: column;
}

.payment-table-scroll {
  overflow-y: auto;
  max-height: 260px;
}

.payment-table-scroll table {
  width: 100%;
}

.payment-table-scroll thead th {
  position: sticky;
  top: 0;
  background: #f9fafb;
  z-index: 1;
}


table {
  width: 100%;
  border-collapse: collapse;
  overflow-y: scroll;
  max-height: 50px;
}

thead {
  background: #f9fafb;
}

th {
  padding: 14px 16px;
  text-align: center;
  font-size: 0.78rem;
  font-weight: 700;
  color: #6b7280;
  text-transform: uppercase;
  letter-spacing: 0.04em;
  border-bottom: 1px solid #e5e7eb;
}

td {
  padding: 14px 16px;
  font-size: 0.92rem;
  color: #111827;
  border-bottom: 1px solid #f3f4f6;
  text-align: center;
}

tbody tr:last-child td {
  border-bottom: none;
}

tbody tr:hover {
  background: #fafafa;
}

.status-pill {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 78px;
  padding: 4px 10px;
  border-radius: 999px;
  font-size: 0.75rem;
  font-weight: 600;
}

.status-pill.paid {
  background: #dcfce7;
  color: #166534;
}

.status-pill.pending {
  background: #fee2e2;
  color: #991b1b;
}

td label {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  margin-right: 14px;
  font-size: 0.9rem;
  color: #374151;
  cursor: pointer;
}

input[type="radio"] {
  margin: 0;
  cursor: pointer;
}

.sec2-buttons {
  display: flex;
  justify-content: flex-end;
  gap: 12px;
}

.sec2-buttons button {
  border: none;
  border-radius: 10px;
  padding: 10px 16px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.15s ease;
}

.sec2-buttons button:first-child {
  background: #dcfce7;
  color: #166534;
}

.sec2-buttons button:first-child:hover {
  background: #a7f3d0;
}

.sec2-buttons .danger {
  background: #fee2e2;
  color: #991b1b;
}

.sec2-buttons .danger:hover {
  background: #fecaca;;
}



.payment-status-group {
  display: flex;
  align-items: center;
  gap: 32px;
  flex-wrap: wrap;
}

.status-option {
  display: flex;
  align-items: center;
  gap: 6px;
  cursor: pointer;
}

.status-option input[type="radio"] {
  margin: 0;
  cursor: pointer;
}

.status-option input[type="radio"]:disabled {
  cursor: not-allowed;
}

.status-option .status-pill {
  transition: transform 0.15s ease, opacity 0.15s ease;
}

.status-option:hover .status-pill {
  transform: translateY(-1px);
}

.status-option input[type="radio"]:disabled + .status-pill {
  opacity: 0.85;
}
</style>