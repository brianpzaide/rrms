<template>
    <span :class="selectedId===patron.id? 'selected': ''">
        <td>{{ patron.seat }}</td>
        <td>{{ patron.name + " " + patron.name + " " + patron.name }}</td>
        <td>
          <span :class="['status-pill', {'paid': patron.payment_status, 'pending': !patron.payment_status}]">
          {{patron.payment_status? 'paid': 'pending'}}
          </span>
        </td>
    </span>
</template>

<script setup>
const props = defineProps({
  patron: {
    type: Object,
    required: true
  },
  selectedId:{
    type: String,
  }
})
</script>


<style scoped>

  tbody tr {
    cursor: pointer;
  }

  tbody tr.selected {
    background-color: #dbeafe;
  }

  tbody tr:hover {
    background: #f1f5f9;
  }
</style>