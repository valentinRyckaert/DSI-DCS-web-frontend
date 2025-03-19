<template>
  <div class="container">
    <TopApplications />
    <ClientEvolution />
    <ProductEvolution />
  </div>
</template>

<script>
import TopApplications from './components/TopApplications.vue';
import ClientEvolution from './components/ClientEvolution.vue';
import ProductEvolution from './components/ProductEvolution.vue';

export default {
  components: {
    TopApplications,
    ClientEvolution,
    ProductEvolution
  }
};
</script>