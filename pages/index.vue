<template>
  <Header />
  <div class="py-10 flex justify-center items-center">
    <div class="container max-w-screen-xl mx-auto p-4">
      <div class="relative ma-0 pa-0">
        <input
          type="text"
          v-model="searchQuery"
          placeholder="Pesquise por nome ou código"
          class="mb-10 px-2 py-6 w-full border rounded-full text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
        />
        <button
          v-if="searchQuery"
          @click="clearSearch"
          class="absolute right-2 top-1/4 transform -translate-y-1/4 pr-3 text-gray-500 hover:text-gray-700"
        >
          X
        </button>
      </div>
      <LoadSpinner v-if="isLoading" />
      <div class="space-y-4">
        <h1 class="text-2xl font-bold">Pokémons</h1>
        <div
          v-if="filteredPokemon.length > 0 && !isLoading"
          class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-4"
        >
          <PokemonCard
            v-for="pokemon in filteredPokemon"
            :key="pokemon.name"
            :pokemon="pokemon"
          />
        </div>
        <div
          v-if="filteredPokemon.length === 0 && !isLoading"
          class="text-center text-xl pt-20 text-gray-600"
        >
          Nenhum Pokémon encontrado!
          <p class="text-sm pt-2">
            Desenvolvido por
            <a
              href="https://github.com/andre26z/pokemon---This-is-a-challenge-by-Coodesh"
            >
              André Santos</a
            >
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from "vue";
import { usePokemonStore } from "~/stores/usePokemonStore";
import Header from "@/components/Header.vue";
import LoadSpinner from "@/components/LoadSpinner.vue";

const pokemonStore = usePokemonStore();
const searchQuery = ref("");
const isLoading = ref(false);

const filteredPokemon = computed(() => {
  const lowerSearchQuery = searchQuery.value.toLowerCase();
  return pokemonStore.pokemonList.filter((pokemon) => {
    const nameMatches = pokemon.name.toLowerCase().includes(lowerSearchQuery);
    const details = pokemonStore.pokemonDetails[pokemon.url];
    const gameIndexMatches = details && details.gameIndex !== undefined && 
                             details.gameIndex.toString().includes(lowerSearchQuery);
    return nameMatches || gameIndexMatches;
  });
});

const clearSearch = () => {
  searchQuery.value = "";
};

const loadMorePokemon = () => {
  if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {
    pokemonStore.fetchPokemon();
  }
};

onMounted(() => {
  isLoading.value = true;
  pokemonStore.fetchPokemon().finally(() => {
    isLoading.value = false;
  });

  if (process.client) {
    window.addEventListener("scroll", loadMorePokemon);
  }
});

onUnmounted(() => {
  if (process.client) {
    window.removeEventListener("scroll", loadMorePokemon);
  }
});
</script>

<style scoped>
html {
  background-color: #04b3ee;
}
</style>
