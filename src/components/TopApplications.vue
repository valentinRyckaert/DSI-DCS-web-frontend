<template>
    <div class="top-applications">
      <h2>Top 10 des applications par grand client</h2>
      <ul>
        <li v-for="(app, index) in topApplications" :key="index">
          {{ app.nomAppli }} - {{ app.total }} €
        </li>
      </ul>
    </div>
</template>

<script>
export default {
    data() {
        return {
            topApplications: []
        }
    },
    mounted() {
        Promise.all([
            fetch('http://localhost:3000/api/applications'),
            fetch('http://localhost:3000/api/lignesFacturation'),
            fetch('http://localhost:3000/api/gdclients')
        ])
        .then(async (responses) => {
            const [applicationsResponse, facturationResponse, gdclientsResponse] = responses;
            const applications = await applicationsResponse.json();
            const facturations = await facturationResponse.json();
            const gdclients = await gdclientsResponse.json();

            // Calculer le top 10 des applications par grand client
            const topApps = this.calculateTopApplications(applications, facturations, gdclients);
            this.topApplications = topApps;
        })
        .catch(error => {
            console.error("Erreur lors de la récupération des données:", error);
        });
    },
    methods: {
        calculateTopApplications(applications, facturations, gdclients) {
            // Logique pour calculer le top 10 des applications par grand client
            const appMap = new Map();

            facturations.forEach(fact => {
                const app = applications.find(a => a.IRT === fact.IRT);
                const gdclient = gdclients.find(gc => gc.GrandClientID === fact.GrandClientID);

                if (app && gdclient) {
                    const key = `${app.nomAppl}-${gdclient.NomGrandClient}`;
                    if (!appMap.has(key)) {
                        appMap.set(key, { nomAppl: app.nomAppl, total: 0 });
                    }
                    appMap.get(key).total += fact.volume;
                }
            });

            // Trier et obtenir le top 10
            const sortedApps = Array.from(appMap.values()).sort((a, b) => b.total - a.total).slice(0, 10);
            return sortedApps;
        }
    }
}
</script>
