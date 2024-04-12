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
          @click="searchQuery = ''"
          class="absolute right-3 top-1/3 transform -translate-y-1/2 text-gray-500 hover:text-gray-700"
        ></button>
      </div>

      <div class="space-y-4">
        <h1 class="text-2xl font-bold">Pokémons</h1>
        <div
          class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-4"
        >
          <PokemonCard
            v-for="pokemon in filteredPokemon"
            :key="pokemon.name"
            :pokemon="pokemon"
          />
        </div>
        <div v-if="pokemonStore.isLoading" class="text-center">
          Carregando mais pokemons...
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, computed } from "vue";
import { usePokemonStore } from "~/stores/usePokemonStore";
import Header from "@/components/Header.vue";

const pokemonStore = usePokemonStore();
const searchQuery = ref("");

const filteredPokemon = computed(() => {
  const lowerSearchQuery = searchQuery.value.toLowerCase();
  return pokemonStore.pokemonList.filter((pokemon) => {
    const nameMatches = pokemon.name.toLowerCase().includes(lowerSearchQuery);
    const gameIndexMatches =
      pokemon.gameIndex !== undefined &&
      pokemon.gameIndex.toString().includes(lowerSearchQuery);
    return nameMatches || gameIndexMatches;
  });
});

onMounted(() => {
  if (pokemonStore.pokemonList.length === 0) {
    pokemonStore.fetchPokemon();
  }
});
</script>
<style scoped>
html {
  background-color: #04b3ee;
}
</style>