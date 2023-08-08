<template>
    <div>
      <ul>
        <li v-for="pokemon in pokemonData" :key="pokemon.name">
          <h2>{{ pokemon.name }}</h2>
          <img :src="pokemon.imageUrl" :alt="pokemon.name" />
          <p>Weight: {{ pokemon.desc }}</p>
          <p>XP: {{ pokemon.exp }}</p>
          <p>Type: {{ pokemon.types.join(', ') }}</p>
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  import { ref, onMounted } from 'vue';
  
  export default {
    setup() {
      const pokemonData = ref([]);
  
      onMounted(async () => {
        try {
          const res = await fetch("https://pokeapi.co/api/v2/pokemon?limit=30");
          const data = await res.json();
  
          const fetchedPokemonData = await Promise.all(
            data.results.map(async (pokemon) => {
              const res = await fetch(pokemon.url);
              const pokemonData = await res.json();
              const typeNames = pokemonData.types.map((type) => type.type.name);
  
              return {
                pokemonData: pokemonData,
                name: pokemon.name,
                imageUrl: pokemonData.sprites.other["official-artwork"].front_default,
                desc: pokemonData.weight,
                exp: pokemonData.base_experience,
                types: typeNames,
              };
            })
          );
  
          pokemonData.value = fetchedPokemonData;
        } catch (error) {
          console.error('Error fetching data:', error);
        }
      });
  
      return {
        pokemonData,
      };
    },
  };
  </script>
  
  <style>
body{
  background-color: black;
  color: white;
  margin: 0;
  
}
ul{
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  gap: 2rem;
}
li{
  border:1px solid white;
  padding:1rem;
  display: flex;
  flex-direction: column;
  align-items: center;
}
h2{
  text-transform: uppercase;
}
img{
  width: 100%;
}

div{
  margin: 2rem;
}
  </style>
  