<template>
  <div class="client-evolution">
    <h2>Évolution des montants pour les 5 plus grands clients</h2>
    <canvas ref="chartCanvas"></canvas>
  </div>
</template>

<script>
import { Chart } from 'chart.js/auto';

export default {
  data() {
      return {
          clients: [],
          facturations: [],
          centres: [],
          labels: [
              'Janvier 2021', 'Février 2021', 'Mars 2021', 'Avril 2021',
              'Mai 2021', 'Juin 2021', 'Juillet 2021', 'Août 2021',
              'Septembre 2021', 'Octobre 2021', 'Novembre 2021', 'Décembre 2021',
              'Janvier 2022', 'Février 2022', 'Mars 2022', 'Avril 2022'
          ]
      };
  },
  mounted() {
    Promise.all([
        fetch('/api/clients'),
        fetch('/api/lignesFacturation'),
        fetch('/api/centreactivites')
    ])
    .then(async (responses) => {
        const [clientsResponse, facturationResponse, centreactivites] = responses;
        const clients = await clientsResponse.json();
        const facturations = await facturationResponse.json();
        const centres = await centreactivites.json()
        this.clients = clients.clients;
        this.facturations = facturations.lignesFacturation;
        this.centres = centres.centresActivite
        this.renderChart();
    })
    .catch(error => {
        console.error("Erreur lors de la récupération des données:", error);
    });
  },
  methods: {
      renderChart() {
          const ctx = this.$refs.chartCanvas.getContext('2d');

          // Identifier les 5 plus grands clients
          const topClients = this.getTopClients(5);

          const datasets = topClients.map(client => {
              return {
                  label: client.NomClient,
                  data: this.getMonthlyData(client),
                  borderColor: this.getRandomColor(),
                  fill: false,
              };
          });

          new Chart(ctx, {
              type: 'line',
              data: {
                  labels: this.labels,
                  datasets: datasets,
              },
              options: {
                  responsive: true,
                  scales: {
                      x: {
                          beginAtZero: true
                      },
                      y: {
                          beginAtZero: true
                      }
                  }
              }
          });
      },
      getTopClients(count) {
          // Logique pour obtenir les 5 plus grands clients
          const clientTotals = {};
          this.facturations.forEach(fact => {
              if (!clientTotals[fact.ClientID]) {
                  clientTotals[fact.ClientID] = 0;
              }
              clientTotals[fact.ClientID] += fact.volume;
          });

          const sortedClients = this.clients
              .map(client => ({ ...client, total: clientTotals[client.ClientID] || 0 }))
              .sort((a, b) => b.total - a.total)
              .slice(0, count);

          return sortedClients;
      },
      getMonthlyData(client) {
          // Logique pour obtenir les données mensuelles pour un client
          const monthlyData = Array(16).fill(0); // Initialiser avec 16 mois
          this.facturations.forEach(fact => {
              if (fact.CentreActiviteID === client.CentreActiviteID) {
                  const monthIndex = this.labels.indexOf(this.getMonthIndex(fact.mois))
                  monthlyData[monthIndex] += fact.volume
              }
          })
          console.log(monthlyData)
          return monthlyData;
      },
      getMonthIndex(dateString) {
          const year = dateString.split('-')[0]
          const month = parseInt(dateString.split('-')[1])
          switch(month){
            case 1: return `Janvier ${year}`; break
            case 2: return `Février ${year}`; break
            case 3: return `Mars ${year}`; break
            case 4: return `Avril ${year}`; break
            case 5: return `Mai ${year}`; break
            case 6: return `Juin ${year}`; break
            case 7: return `Juillet ${year}`; break
            case 8: return `Août ${year}`; break
            case 9: return `Septembre ${year}`; break
            case 10: return `Octobre ${year}`; break
            case 11: return `Novembre ${year}`; break
            case 12: return `Decembre ${year}`; break
          }
      },
      getRandomColor() {
          const letters = '0123456789ABCDEF';
          let color = '#';
          for (let i = 0; i < 6; i++) {
              color += letters[Math.floor(Math.random() * 16)];
          }
          return color;
      }
  }
}
</script>
