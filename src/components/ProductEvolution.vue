<template>
    <div class="product-evolution">
      <h2>Évolution des volumes des produits 1_1 et 1_4</h2>
      <canvas ref="chartCanvas"></canvas>
    </div>
  </template>
  
<script>
  import axios from 'axios';
  import { Chart } from 'chart.js';
  
  export default {
    data() {
      return {
        products: []
      };
    },
    mounted() {
      axios.get('http://localhost:3000/api/produits')
        .then(response => {
          this.products = response.data;
          this.renderChart();
        })
        .catch(error => {
          console.error("Erreur lors de la récupération des produits:", error);
        });
    },
    methods: {
      renderChart() {
        const ctx = this.$refs.chartCanvas.getContext('2d');
        const product1_1 = this.products.find(product => product.id === '1_1');
        const product1_4 = this.products.find(product => product.id === '1_4');
  
        new Chart(ctx, {
          type: 'line',
          data: {
            labels: ['Janvier 2021', 'Février 2021', 'Mars 2021', 'Avril 2021', 'Mai 2021', 'Juin 2021', 'Juillet 2021', 'Août 2021', 'Septembre 2021', 'Octobre 2021', 'Novembre 2021', 'Décembre 2021', 'Janvier 2022', 'Février 2022', 'Mars 2022', 'Avril 2022'],
            datasets: [
              {
                label: 'Produit 1_1',
                data: product1_1 ? product1_1.volumes : [],
                borderColor: 'rgba(75, 192, 192, 1)',
                fill: false,
              },
              {
                label: 'Produit 1_4',
                data: product1_4 ? product1_4.volumes : [],
                borderColor: 'rgba(153, 102, 255, 1)',
                fill: false,
              },
            ],
          },
        });
      }
    }
  };
</script>
  