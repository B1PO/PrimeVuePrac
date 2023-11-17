<template>
    <div class="card">
        <h5>Datos hstóricos del mercado de valores de Lyon, Francia</h5>
      <Chart type="line" :data="chartData" :options="chartOptions" />
    </div>
    <div class="card">
        <h5>Cambio porcentual por día de acciones</h5>
      <DataTable :value="stockData" class="p-datatable-gridlines p-datatable-striped">
        <Column field="date" header="Fecha"></Column>
        <Column field="open" header="Valor Apertura"></Column>
        <Column field="close" header="Valor Cierre"></Column>
      </DataTable>
    </div>
  </template>
  
  <script>
  import Chart from 'primevue/chart';
  import axios from 'axios';
  import DataTable from 'primevue/datatable';
  import Column from 'primevue/column';
  
  export default {
    components: {
      Chart,
      DataTable,
      Column
    },
    data() {
      return {
        chartData: null,
        chartOptions: null,
        stockData: null
      };
    },
    created() {
      this.fetchData();
    },
    methods: {
      fetchData() {
        const apiUrl = 'https://eodhistoricaldata.com/api/eod/MCD.US?from=2017-01-05&to=2017-02-05&period=d&fmt=json&api_token=OeAFFmMliFG5orCUuwAKQ8l4WWFQ67YX';
        axios.get(apiUrl)
          .then(response => {
            this.stockData = response.data;
            this.chartData = this.transformData(this.stockData);
            this.setChartOptions();
          })
          .catch(error => {
            console.error("There was an error fetching the chart data:", error);
          });
      },
      transformData(data) {
        const labels = data.map(item => item.date);
        const values = data.map(item => item.close);
  
        return {
          labels,
          datasets: [
            {
              label: 'Cambio porcentual trimestral',
              data: values,
              fill: false,
              borderColor: '#42A5F5',
              tension: 0.4
            }
          ]
        };
      },
      setChartOptions() {
        this.chartOptions = {
          title: {
              display: true,
              text: 'Cambio porcentual por día de acciones AAPL'
          },
          scales: {
            yAxes: [{
              ticks: {
                callback(value) {
                  return '$' + value;
                }
              }
            }]
          },
          responsive: true,
          maintainAspectRatio: false
        };
      }
    }
  };
  </script>
  
  <style>
  .p-datatable .p-datatable-thead > tr > th {
      background-color: #f5f5f5; 
      color: #495057; 
      font-weight: bold;
  }
  .p-datatable .p-datatable-tbody > tr > td {
      border-bottom: 1px solid #dee2e6; 
  }
  .p-datatable-striped .p-datatable-tbody > tr:nth-child(odd) {
      background-color: #f9f9f9; 
  }
  </style>
  