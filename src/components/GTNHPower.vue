<script setup>
import {ref} from 'vue'

const API_URL = 'http://127.0.0.1:8000/gtnhpower/api'

const gtnhPower = ref(null)

const fetchGtnhPower = async (timeframe, server) => {
  const response = await fetch(API_URL + `?timeframe=${timeframe}&server=${server}`)
  //gtnhPower.value = await response.json()
  return await response.json()
}

async function updateDataFromApi() {
  const series = await fetchGtnhPower(24, "niels_gregblock");

  data.value = {
    ...data.value,
    series: [{
      data: series.map((element) => element.eu)
    },
      {
        data: series.map((element) => element.mspt)
      }],
    chartOptions: {
      ...data.value.chartOptions,
      xaxis: {
        ...data.value.chartOptions.xaxis,
        categories: series.map((element) => element.time)
      }
    }
  }

}


const data = ref({
  chartOptions: {
    grid: {
      show: true,
      borderColor: 'rgba(255,255,255,0.24)',
    },
    tooltip: {
      theme: 'dark',
      shared: false,
      intersect: true,
    },
    chart: {
      id: "vuechart-example",
      width: 1000,
      height: 800,
      type: 'line',
    },
    title: {
      text: 'GTNH Power',
      align: 'middle',
      //font color white
      style: {
        color: '#ffffff',
        fontSize: '0px',
      },
    },
    stroke: {
      width: 3,
      curve: 'smooth'
    },
    xaxis: {
      axisBorder: {
        show: false,
      },
      axisTicks: {
        show: false,
      },
      type: 'datetime',
      categories: [],
      tickAmount: 8,
      tickPlacement: "on",
      labels: {
        formatter: function (value, timestamp, opts) {
          return opts.dateFormatter(new Date(timestamp), 'HH:mm')
        },
        style: {
          colors: '#ffffff',
          fontSize: '16px',
        },
      },
    },
    yaxis: [
      {
        min: 0,
        tickAmount: 8,
        axisBorder: {
          show: false,
        },
        axisTicks: {
          show: false,
        },
        labels: {
          //exponential format
          formatter: function (value) {
            return value.toExponential(1);
          },
          style: {
            colors: '#ffffff',
            fontSize: '16px',
          },
        },
      },
      {
        min: 0,
        max: 200,
        opposite: true,
        tickAmount: 8,
        axisBorder: {
          show: false,
        },
        axisTicks: {
          show: false,
        },

        labels: {
          //exponential format
          formatter: function (value) {
            return value.toFixed(0);
          },
          style: {
            colors: '#ffffff',
            fontSize: '16px',
          },
        },
      }
    ]
  },
  series: [{
    name: 'EU',
    data: []
  },
    {
      name: 'MSPT',
      data: []
    }],


})

</script>

<template>
  <div>
    <h1>GTNH Power</h1>
    <button @click="updateDataFromApi">Fetch GTNH Power</button>
    <div>
      <apexchart
          :width="data.chartOptions.chart.width"
          :height="data.chartOptions.chart.height"
          type="line"
          :options="data.chartOptions"
          :series="data.series"
      ></apexchart>
    </div>
  </div>

</template>

<style scoped>

</style>