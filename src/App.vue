<template>
<!-- ===== Analytics Section ===== -->
<!-- <div class="analytics" id="analyticsSection">

  <div class="analytics-header" onclick="toggleAnalytics()">
    <span>📊 Analytics</span>
    <span id="analyticsToggleIcon">▲</span>
  </div>

  <div class="analytics-body" id="analytics-body">
    {% include 'templates/analytics.html' %}
  </div>
</div> -->

  <div class="container">
    <div class="sec1">
      <LeftPanel/>
    </div>

    <div class="sec2">
      <RightPanel :selectedPatron="selectedPatron"/>
    </div>
  </div>
</template>


<script setup>

  import LeftPanel from './components/LeftPanel.vue'
  import RightPanel from './components/RightPanel.vue'

  import { ref } from "vue"

  const selectedPatron = ref(null)
  
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

  // function initAnalyticsCharts(root = document) {
  //   const container = root.querySelector("#analyticsCharts");
  //   if (!container) return;

  //   const monthlySeries = JSON.parse(container.dataset.monthlySeries);
  //   const months = JSON.parse(container.dataset.months);
  //   const yearlySeries = JSON.parse(container.dataset.yearlySeries);
  //   const years = JSON.parse(container.dataset.years);

  //   monthlyChart?.destroy();
  //   yearlyChart?.destroy();

  //   monthlyChart = new ApexCharts(
  //     document.querySelector("#monthlyRevenueChart"),
  //     {
  //       chart: { type: "bar", stacked: true, height: 300 },
  //       plotOptions: { bar: { horizontal: true } },
  //       series: monthlySeries,
  //       xaxis: { categories: months },
  //       legend: { position: "top" }
  //     }
  //   );

  //   yearlyChart = new ApexCharts(
  //     document.querySelector("#yearlyRevenueChart"),
  //     {
  //       chart: { type: "line", height: 300 },
  //       series: yearlySeries,
  //       xaxis: { categories: years }
  //     }
  //   );

  //   monthlyChart.render();
  //   yearlyChart.render();
  // }

  // document.addEventListener("DOMContentLoaded", initAnalyticsCharts);

  // document.body.addEventListener("htmx:afterSwap", (e) => {
  //   if (e.target.id === "analytics-body") {
  //     initAnalyticsCharts(e.target);
  //   }
  // });

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

  h1, h3 {
    text-align: center;
    margin-bottom: 20px;
  }

  .container {
    display: flex;
    gap: 20px;
    height: calc(100vh - 120px);
    align-items: flex-start; 
  }

  .sec1, .sec2 {
    background: #fff;
    min-width: 0;
    padding: 15px;
    border-radius: 6px;
    box-shadow: 0 2px 6px rgba(0,0,0,0.08);
    display: flex;
    flex-direction: column;
    height: 755px;
  }

  

  .sec1 { flex: 1; }
  .sec2 { flex: 1; }

  .sec2-buttons {
    display: flex;
    justify-content: space-between;
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


</style>
