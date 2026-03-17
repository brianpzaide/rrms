<template>
<!-- ===== Analytics Section ===== -->
<div class="analytics" id="analyticsSection">

  <div class="analytics-header" onclick="toggleAnalytics()">
    <span>📊 Analytics</span>
    <span id="analyticsToggleIcon">▲</span>
  </div>

  <div class="analytics-body" id="analytics-body">
    {% include 'templates/analytics.html' %}
  </div>
</div>

<div class="container">
  <div class="sec1">
    <LeftPanel/>
  </div>

  <div class="sec2">

    <div class="tabs">
      <div class="tab active" id="detailsTabBtn" onclick="switchTab('details')">Patron Details</div>
      <div class="tab" id="addTabBtn" onclick="switchTab('add')">Add Patron</div>
    </div>

    <!-- <div id="detailsTab" class="tab-content active">
      <div class="l2s1">
        <h3>{{selected_patron.name}}</h3>
        <div class="detail-row"><strong>Phone:</strong> {{selected_patronpatron.phone}}</div>
        <div class="detail-row"><strong>Email:</strong> {{selected_patronpatron.email}}</div>
        <div class="detail-row"><strong>Joining Date:</strong> {{selected_patronpatron.start_date}}</div>
        <div class="detail-row"><strong>Seat:</strong> {{selected_patronpatron.seat}}</div>
        <div class="detail-row"><strong>Amount:</strong> {{selected_patronpatron.amount}}</div>
        <div class="detail-row"><strong>Category:</strong> {{selected_patronpatron.category}}</div>
        <div class="detail-row"><strong>Payment Status:</strong> {{selected_patronpatron.payment_status}}</div>
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
            {% for payment in payment_history %}
              <tr>
                <td>{{payment.payment_date}}</td>
                <td>{{payment.amount}}</td>
                <td>Paid</td>
              </tr>
            {%endfor%}
          </tbody>
        </table>
      </div>

      <div class="sec2-buttons">
        <button>Send email reminder</button>
        <button class="danger">Terminate Contract</button>
      </div>
    </div> -->

    <div id="addTab" class="tab-content">
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
        <label>Amount</label>
        <input type="number">
      </div>

      <button>Add Patron</button>
    </div>

  </div>
</div>
</template>


<script setup>

  import LeftPanel from './components/LeftPanel.vue'


  function switchTab(tab) {
    document.querySelectorAll(".tab").forEach(t => t.classList.remove("active"));
    document.querySelectorAll(".tab-content").forEach(c => c.classList.remove("active"));

    if (tab === "add") {
      document.getElementById("addTabBtn").classList.add("active");
      document.getElementById("addTab").classList.add("active");
    } else {
      document.getElementById("detailsTabBtn").classList.add("active");
      document.getElementById("detailsTab").classList.add("active");
    }
  }

  function openAddPatron() {
    switchTab("add");
  }

  function openPatronDetails() {
    switchTab("details");
  }

  function selectPatronRow(row) {
    document
      .querySelectorAll(".l2 tbody tr")
      .forEach(r => r.classList.remove("selected"));

    row.classList.add("selected");
    openPatronDetails();
  }
  
  
  let monthlyChart = null;
  let yearlyChart = null;

  function toggleAnalytics() {
    const section = document.getElementById("analyticsSection");
    const icon = document.getElementById("analyticsToggleIcon");

    section.classList.toggle("collapsed");
    icon.textContent = section.classList.contains("collapsed") ? "▼" : "▲";

    if (!section.classList.contains("collapsed")) {
      setTimeout(() => {
        monthlyChart?.resize();
        yearlyChart?.resize();
      }, 300);
    }
  }

  function initAnalyticsCharts(root = document) {
    const container = root.querySelector("#analyticsCharts");
    if (!container) return;

    const monthlySeries = JSON.parse(container.dataset.monthlySeries);
    const months = JSON.parse(container.dataset.months);
    const yearlySeries = JSON.parse(container.dataset.yearlySeries);
    const years = JSON.parse(container.dataset.years);

    monthlyChart?.destroy();
    yearlyChart?.destroy();

    monthlyChart = new ApexCharts(
      document.querySelector("#monthlyRevenueChart"),
      {
        chart: { type: "bar", stacked: true, height: 300 },
        plotOptions: { bar: { horizontal: true } },
        series: monthlySeries,
        xaxis: { categories: months },
        legend: { position: "top" }
      }
    );

    yearlyChart = new ApexCharts(
      document.querySelector("#yearlyRevenueChart"),
      {
        chart: { type: "line", height: 300 },
        series: yearlySeries,
        xaxis: { categories: years }
      }
    );

    monthlyChart.render();
    yearlyChart.render();
  }

  document.addEventListener("DOMContentLoaded", initAnalyticsCharts);

  document.body.addEventListener("htmx:afterSwap", (e) => {
    if (e.target.id === "analytics-body") {
      initAnalyticsCharts(e.target);
    }
  });

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


</script>



<style scoped>
* {
    box-sizing: border-box;
    font-family: Arial, sans-serif;
  }

  body {
    margin: 0;
    padding: 20px;
    background: #f4f6f8;
  }

  h1, h3 {
    text-align: center;
    margin-bottom: 20px;
  }

  .container {
    display: flex;
    gap: 20px;
    height: calc(100vh - 120px);
  }

  .sec1, .sec2 {
    background: #fff;
    min-width: 0;
    padding: 15px;
    border-radius: 6px;
  }

  .sec1 { flex: 1; }
  .sec2 { flex: 1; }

  .l2 {
    flex: 1;
    overflow: auto;
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

  tbody tr.selected {
    background-color: #dbeafe;
  }

  tbody tr:hover {
    background: #f1f5f9;
  }

  .tabs {
    display: flex;
    border-bottom: 1px solid #ccc;
  }

  .tab {
    padding: 10px 15px;
    cursor: pointer;
    background: #f0f0f0;
    border: 1px solid #ccc;
    border-bottom: none;
    margin-right: 5px;
    border-radius: 4px 4px 0 0;
  }

  .tab.active {
    background: #fff;
    font-weight: bold;
  }

  .tab-content {
    display: none;
    flex: 1;
    overflow: auto;
    gap: 15px;
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

  .form-group {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  .form-group input {
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  button {
    padding: 8px 12px;
    border: none;
    border-radius: 4px;
    background: #007bff;
    color: white;
    cursor: pointer;
  }

  button.secondary { background: #6c757d; }
  button.danger { background: #dc3545; }

  .analytics {
    background: #ffffff;
    border-radius: 6px;
    padding: 0;
    margin-bottom: 20px;
    overflow: hidden;
  }

  .analytics-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 12px 16px;
    cursor: pointer;
    background: #f0f4f8;
    font-weight: bold;
  }

  .analytics-body {
    padding: 16px;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }

  .analytics.collapsed .analytics-body {
    display: none;
  }

  .analytics-metrics {
    display: flex;
    gap: 20px;
    flex-wrap: wrap;
  }

  .metric-box {
    background: #f9fafb;
    padding: 12px 16px;
    border-radius: 6px;
    min-width: 220px;
    font-weight: 600;
  }

  .analytics-charts {
    display: flex;
    gap: 20px;
    flex-wrap: wrap;
  }

  .chart-box {
    flex: 1;
    min-width: 300px;
    background: #ffffff;
    border: 1px solid #e5e7eb;
    border-radius: 6px;
    padding: 10px;
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
</style>
