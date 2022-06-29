<script setup>
import navbar from "./components/navbar.vue";
import foot from "./components/footer.vue";
import cryptos from "./components/cryptos.vue";
import balance from "./components/balance.vue";
import { ref } from "@vue/reactivity";

const openMoves = ref(false);
</script>

<template>
  <navbar class="sticky top-0 z-40" @openMoves="openMoves = true" />
  <cryptos />
  <balance
    v-if="openMoves"
    @close="openMoves = false"
    class="absolute inset-0 z-10"
  />
  <foot />
</template>
