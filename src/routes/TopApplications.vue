<template>
    <div class="container mt-5">
        <h2 class="text-center mb-4">Top 10 des applications par grand client</h2>
        <div v-for="(clientApps, clientName) in topApplicationsByClient" :key="clientName" class="mb-5">
            <h3>{{ clientName }}</h3>
            <div class="table-responsive">
                <table class="table table-striped table-hover">
                    <thead class="thead-dark">
                        <tr>
                            <th scope="col">#</th>
                            <th scope="col">Nom de l'application</th>
                            <th scope="col">Montant (€)</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(app, index) in clientApps" :key="index">
                            <th scope="row">{{ index + 1 }}</th>
                            <td>{{ app.nomAppli }}</td>
                            <td>{{ Math.floor(app.total) }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            topApplicationsByClient: {}
        }
    },
    mounted() {
        Promise.all([
            fetch('/api/applications'),
            fetch('/api/lignesFacturation'),
            fetch('/api/gdclients')
        ])
        .then(async (responses) => {
            const [applicationsResponse, facturationResponse, gdclientsResponse] = responses;
            const applications = await applicationsResponse.json();
            const facturations = await facturationResponse.json();
            const gdclients = await gdclientsResponse.json();
            // Calculer le top 10 des applications par grand client
            const topAppsByClient = this.calculateTopApplications(
                applications.applications,
                facturations.lignesFacturation,
                gdclients.grandclients
            );
            this.topApplicationsByClient = topAppsByClient;
        })
        .catch(error => {
            console.error("Erreur lors de la récupération des données:", error);
        });
    },
    methods: {
        calculateTopApplications(applications, facturations, gdclients) {
            const topAppsByClient = {};

            gdclients.forEach(gdclient => {
                const appMap = new Map();

                facturations.forEach(fact => {
                    if (fact.GrandClientID === gdclient.GrandClientID) {
                        const app = applications.find(a => a.IRT === fact.IRT);

                        if (app) {
                            if (!appMap.has(app.nomAppli)) {
                                appMap.set(app.nomAppli, { nomAppli: app.nomAppli, total: 0 });
                            }
                            appMap.get(app.nomAppli).total += fact.volume;
                        }
                    }
                });

                // Trier et obtenir le top 10 pour ce client
                const sortedApps = Array.from(appMap.values())
                                         .sort((a, b) => b.total - a.total)
                                         .slice(0, 10);
                topAppsByClient[gdclient.NomGrandClient] = sortedApps;
            });

            return topAppsByClient;
        }
    }
}
</script>
