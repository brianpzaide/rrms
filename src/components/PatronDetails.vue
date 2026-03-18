<template>
  <div class="tab-content active">
    <div class="l2s1">
      <h3>{{selectedPatron.name}}</h3>
      <div class="detail-row"><strong>Phone:</strong> {{selectedPatron.phone}}</div>
      <div class="detail-row"><strong>Email:</strong> {{selectedPatron.email}}</div>
      <div class="detail-row"><strong>Joining Date:</strong> {{selectedPatron.start_date}}</div>
      <div class="detail-row"><strong>Seat:</strong> {{selectedPatron.seat}}</div>
      <div class="detail-row"><strong>Amount:</strong> {{selectedPatron.amount}}</div>
      <div class="detail-row"><strong>Category:</strong> {{selectedPatron.category}}</div>
      <div class="detail-row"><strong>Payment Status:</strong> {{selectedPatron.payment_status}}</div>
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
            <td>
              <span :class="['status-pill', {'paid': payment.status, 'pending': !payment.status}]">
                {{payment.status ? 'paid': 'pending'}}
              </span>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="sec2-buttons">
      <button class="danger"
        @click="terminateContract"
      >
        Terminate Contract
      </button>
    </div>
  </div>
</template>

<script setup>
  const props = defineProps({
    selectedPatron: {
      type: Object
    },
  })

  const emit = defineEmits([
    "terminate-contract"
  ])

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
  }

  .detail-row {
    margin-bottom: 6px;
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
  }

  button.danger { background: #dc3545; }
</style>