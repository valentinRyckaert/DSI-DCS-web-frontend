<template>
    <div class="client-evolution">
      <h2>Évolution des montants pour les 5 plus grands clients</h2>
      <canvas ref="chartCanvas"></canvas>
    </div>
</template>
  
<script>
  
  import { Chart } from 'chart.js'
  
  export default {
    data() {
      return {
        clients: []
      }
    },
    mounted() {
      fetch('http://localhost:3000/api/clients')
        .then(response => {
          this.clients = response.data
          this.renderChart()
        })
        .catch(error => {
          console.error("Erreur lors de la récupération des clients:", error)
        })
    },
    methods: {
      renderChart() {
        const ctx = this.$refs.chartCanvas.getContext('2d')
        new Chart(ctx, {
          type: 'line',
          data: {
            labels: ['Janvier 2021', 'Février 2021', 'Mars 2021', 'Avril 2021', 'Mai 2021', 'Juin 2021', 'Juillet 2021', 'Août 2021', 'Septembre 2021', 'Octobre 2021', 'Novembre 2021', 'Décembre 2021', 'Janvier 2022', 'Février 2022', 'Mars 2022', 'Avril 2022'],
            datasets: this.clients.slice(0, 5).map((client, index) => ({
              label: `Client ${index + 1}`,
              data: client.amounts,
              borderColor: `rgba(${Math.random() * 255}, ${Math.random() * 255}, ${Math.random() * 255}, 1)`,
              fill: false,
            })),
          },
        })
      }
    }
  }
</script>
  