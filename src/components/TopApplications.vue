<template>
    <div class="top-applications">
      <h2>Top 10 des applications par grand client</h2>
      <ul>
        <li v-for="(app, index) in applications" :key="index">{{ app.NomClient }} - 29 €</li>
      </ul>
    </div>
</template>

<script>


export default {
    data() {
        return {
        applications: []
        }
    },
    mounted() {
        fetch(`${import.meta.env.API_URL}/api/applications`, {
            headers: {
                "Content-Type": "application/json",
            }
        })
        .then(response => {
            this.applications = response.data.slice(0, 10)
        })
        .catch(error => {
            console.error("Erreur lors de la récupération des applications:", error)
        })
    }
}
</script>
  