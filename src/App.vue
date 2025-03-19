<template>
  <nav class="navbar navbar-expand-lg bg-body-tertiary">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">DSI</a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav">
          <li class="nav-item">
            <button class="nav-link" aria-current="page" @click="activePage = 0">Top 10 Applications</button>
          </li>
          <li class="nav-item">
            <button class="nav-link" @click="activePage = 1">Features</button>
          </li>
          <li class="nav-item">
            <button class="nav-link" @click="activePage = 2">Pricing</button>
          </li>
        </ul>
      </div>
    </div>
  </nav>
  <div class="m-4">
    <TopApplications v-if="activePage === 0"/>
    <ClientEvolution v-if="activePage === 1"/>
    <ProductEvolution v-if="activePage === 2"/>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import TopApplications from './components/TopApplications.vue'
import ClientEvolution from './components/ClientEvolution.vue'
import ProductEvolution from './components/ProductEvolution.vue'

const activePage = ref(0)
</script>
