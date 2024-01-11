<template>
  <div>
    <canvas width="350" height="350" id="myChart" class="mx-auto"></canvas>
  </div>
</template>

<script setup>
import { onMounted, watchEffect } from 'vue'
import Chart from 'chart.js/auto'

const props = defineProps({
  chartData: {
    type: Array,
    required: true,
  },
})

let myChart

// Function to create the chart
const createChart = (filteredData) => {
  const canvas = document.getElementById('myChart')
  const ctx = canvas.getContext('2d')

  if (myChart) {
    myChart.destroy()
  }

  myChart = new Chart(ctx, {
    type: 'pie',
    data: {
      labels: ['Herren', 'Damen', 'Kinder', 'Babies', 'Keine Information'],
      datasets: [
        {
          backgroundColor: ['#2d6cdf', '#fc5c9c', '#a7ff83', '#fccde2', '#dee1ec'],
          borderColor: '#dee1ec',
          data: filteredData,
          borderWidth: 1,
        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: {
          position: 'top',
          labels: {
            font: {
              size: 14,
            },
          },
        },
        tooltip: {
          callbacks: {
            label: function (tooltipItem) {
              const data = tooltipItem.dataset.data
              const total = data.reduce((acc, value) => acc + value, 0)
              const currentValue = tooltipItem.raw
              const percentage = Math.round((currentValue / total) * 100)

              return `${tooltipItem.label}: ${tooltipItem.raw} - ${percentage}%`
            },
          },
        },
      },
    },
  })
}

onMounted(() => {
  createChart(getChartData(props.chartData))

  // Watch for changes in chartData and recreate the chart
  watchEffect(() => {
    createChart(getChartData(props.chartData))
  })
})

// Helper function to process chart data
const getChartData = (chartData) => {
  const h = chartData.filter((match) => match.Geschlecht === 'Herren')
  const d = chartData.filter((match) => match.Geschlecht === 'Damen')
  const k = chartData.filter((match) => match.Geschlecht === 'Kinder')
  const b = chartData.filter((match) => match.Geschlecht === 'Babies')
  const x = chartData.filter(
    (match) =>
      match.Geschlecht !== 'Herren' &&
      match.Geschlecht !== 'Damen' &&
      match.Geschlecht !== 'Kinder' &&
      match.Geschlecht !== 'Babies',
  )

  return [h.length, d.length, k.length, b.length, x.length]
}
</script>
