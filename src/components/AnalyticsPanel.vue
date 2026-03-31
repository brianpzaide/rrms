<template>
    <div class="analytics">
        <div class="analytics-header" @click="toggleAnalytics">
            <span>📊 Analytics</span>
            <span>{{ showAnalytics ? "▲" : "▼" }}</span>
        </div>
        <div v-if="showAnalytics"  class="analytics-body">
            <div class="analytics-charts">
                <div class="chart-box">
                  <apexchart
                    type="bar"
                    height="500"
                    :options="monthlyChartOptions"
                    :series="props.monthlyChartSeries"
                  />
                </div>

                <div class="chart-box">
                  <apexchart
                    type="line"
                    height="500"
                    :options="yearlyChartOptions"
                    :series="props.yearlyChartSeries"
                  />
                </div>
            </div>
        </div>
    </div>    
</template>

<script setup>
    import { ref, computed } from "vue"
    import VueApexCharts from "vue3-apexcharts"

    const apexchart = VueApexCharts

    const showAnalytics = ref(false)
    const props = defineProps({
        totalRevenue: {
            type: Number,
            required: true,
            default: 75000
        },
        occupied: {
            type: Number,
            required: true,
            default: 75
        },
        vacant: {
            type: Number,
            required: true,
            default: 25
        },
        monthlyChartSeries: {
            type: Array,
            required: true
        },
        yearlyChartSeries: {
            type: Array,
            required: true
        },
        yearlyChartSeriesAxis: {
            type: Array,
            required: true
        }
    })

    const monthlyChartOptions = computed(() => ({
        // chart: {
        //   stacked: true,
        //   toolbar: {
        //     show: false
        //   }
        // },
        dataLabels: {
          enabled: false,
        },
        plotOptions: {
          bar: {
            horizontal: true,
            dataLabels: {
              position: 'top',
            },
          }
        },
        xaxis: {
            categories: [
                "Jan", "Feb", "Mar", "Apr", "May", "Jun",
                "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
            ]
        },
        legend: {
            position: "top"
        }
    }))

    const yearlyChartOptions = computed(() => ({
        chart: {
            toolbar: {
                show: false
            }
        },
        xaxis: {
            categories: props.yearlyChartSeriesAxis
        },
        stroke: {
            curve: "smooth"
        }
    }))
    
    function toggleAnalytics(){
        showAnalytics.value = !(showAnalytics.value)
    }



    // function renderAnalytics() {
    //   const monthlyChart = new ApexCharts(document.querySelector('#monthlyRevenueCategoryChart'), {
    //     chart: { type: 'bar', stacked: true, height: 300},
    //     series: props.monthyChartSeries,
    //     xaxis: {
    //       categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
    //     }
    //   });

    //   monthlyChart.render();

    //   const yearlyChart = new ApexCharts(document.querySelector('#yearlyRevenueChart'), {
    //     chart: { type: 'line', height: 300 },
    //     series: props.yearlyChartSeries,
    //     xaxis: {
    //       categories: props.yearlyChartSeriesAxis
    //     }
    //   });

    //   yearlyChart.render();
    // }
</script>

<style scoped>
    .analytics {
      background: #fff;
      border-radius: 10px;
      margin-bottom: 20px;
      overflow: hidden;
      border: 1px solid #dbe2ea;
    }

    .analytics-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 14px 18px;
      background: #eef2f6;
      cursor: pointer;
      font-weight: 600;
    }

    .analytics-body {
      padding: 18px;
      display: flex;
      flex-direction: column;
      gap: 18px;
    }

    .analytics.collapsed .analytics-body {
      display: none;
    }

    .analytics-metrics {
      display: flex;
      flex-wrap: wrap;
      gap: 14px;
    }

    .metric-box {
      background: #f9fafb;
      border: 1px solid #e5e7eb;
      border-radius: 8px;
      padding: 14px 16px;
      min-width: 220px;
      font-weight: 600;
    }

    .analytics-charts {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
      gap: 18px;
    }

    .chart-box {
      background: #fff;
      border: 1px solid #e5e7eb;
      border-radius: 8px;
      padding: 10px;
    }
</style>