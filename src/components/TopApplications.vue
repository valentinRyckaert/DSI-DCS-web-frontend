<template>
    <div class="top-applications">
      <h2>Top 10 des applications par grand client</h2>
      <ul>
        <li v-for="(app, index) in applications" :key="index">{{ app.name }} - {{ app.amount }} €</li>
      </ul>
    </div>
</template>

<script>
import axios from 'axios';

export default {
data() {
    return {
    applications: []
    };
},
mounted() {
    axios.get('http://localhost:3000/api/applications')
    .then(response => {
        this.applications = response.data.slice(0, 10);
    })
    .catch(error => {
        console.error("Erreur lors de la récupération des applications:", error);
    });
}
};
</script>
  