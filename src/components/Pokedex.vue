<script setup>
  import { ref, onMounted, watch } from 'vue'

  const searchPokemon = ref(1)
  const formSearchPokemon = ref("")

  const pokemonData = ref([])
  const pokemonStatus = ref([])
  const pokemonNumber = ref("")
  const pokemonName = ref("Loading ...")
  const pokemonImg = ref(null)
  const pokemonAbilities = ref([])
  const pokemonTypes = ref([])

  const renderPokemon = async (pokemon) => {

    resetDataPokemon()

    const data = await getPokemon(pokemon)


    if (data) {

      pokemonNumber.value = data.id
      pokemonName.value = data.name
      pokemonImg.value = data['sprites']['versions']['generation-v']['black-white']['animated']['front_default'];
      pokemonStatus.value = data.stats
      pokemonAbilities.value = data.abilities
      pokemonTypes.value = data.types[0].type

    } else {
      notFoundPokemon()
    }

  }

  const getPokemon = async (pokemon) => {

    let request = `https://pokeapi.co/api/v2/pokemon/${pokemon}`

    try {
      const APIResponse = await axios.get(request);

      if (APIResponse.status === 200){
        pokemonData.value = APIResponse.data;
        return pokemonData.value;
      }

    } catch (error) {
      notFoundPokemon()
    }

  }

  function prevNextPokemon(click){
    switch (click) {
      case 'prev':
        if (searchPokemon.value > 1){
          searchPokemon.value -= 1;
          renderPokemon(searchPokemon.value);
        }
        break;

      case 'next':
        searchPokemon.value += 1;
        renderPokemon(searchPokemon.value);
        break;

      default:
        searchPokemon.value = 1;
        renderPokemon(searchPokemon.value);
        break;
    }
  }

  function resetDataPokemon(){
    pokemonNumber.value = ""
    pokemonName.value = "Loading ..."
    pokemonImg.value = null
    formSearchPokemon.value = ""
  }

  function notFoundPokemon(){
    pokemonNumber.value = ""
    pokemonName.value = "Not found :c"
    pokemonImg.value = null
  }

  function searchFormPokemon(pokemon){
    renderPokemon(pokemon.toLowerCase())
  }

  onMounted(() => {
    renderPokemon(searchPokemon.value)
  })

  defineProps({
    msg: String
  })

</script>

<template>
  <div class="main__template">
    <!-- INFO LEFT -->
    <div>
      <!-- POKEDEX -->
      <img
        class="pokedex"
        src="./../images/pokedex_none_background.png"
        alt="Pokedex" />

      <!-- POKEMON -->
      <img
        v-if="pokemonImg != null"
        class="pokemon__image"
        :src="pokemonImg"
        alt="Pokemon" />

      <!-- INFO POKEMON -->
      <h1 class="pokemon__data">
        <span class="pokemon__number">{{ pokemonNumber }}</span> -
        <span class="pokemon__name">{{ pokemonName }}</span>
      </h1>

      <!-- FORM/INPUT -->
      <form @submit.prevent="searchFormPokemon(formSearchPokemon)" class="form">
        <input
          v-model="formSearchPokemon"
          type="search"
          class="input__search"
          placeholder="Name or Number"
          required />
      </form>

      <!-- BUTTONS -->
      <div class="buttons">
        <button @click="prevNextPokemon('prev')" class="button">Prev</button>
        <button @click="prevNextPokemon('next')" class="button">Next</button>
      </div>
    </div>
    <!-- INFO RIGHT -->
    <div>
      <div class="stats-1-1 flex flex-col text-sm">
        <div v-for="stats in pokemonStatus" :key="stats.stat.name" class="flex flex-col w-full flex-row">
          <div class="grid flex-grow">
            {{ stats.stat.name }} - {{ stats.base_stat }}
          </div>
          <div class="grid flex-grow">
            <progress
              class="stats-2-2 progress progress-primary w-52 grid h-2 rounded-box place-items-center"
              :value="stats.base_stat" max="200"></progress>
          </div>
        </div>
        <div class="grid flex-grow mt-4">
          <button class="btn gap-2">
            Type:
            <div class="badge badge-primary">{{ pokemonTypes.name }}</div>
          </button>
        </div>
      </div>
      <div class="badge-a">
        <div class="badge badge-primary">Height: {{ pokemonData.height }}</div>
      </div>
      <div class="badge-b">
        <div class="badge badge-primary">Weight: {{ pokemonData.weight }}</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Oxanium:wght@300;400;500;600;700;800&display=swap');

  .main__template {
    display: inline-block;
    margin-top: 2%;
    padding: 15px;
    position: relative;
  }

  .pokedex {
    width: 100%;
    max-width: 825px;
  }

  .pokemon__image {
    position: absolute;
    bottom: 51%;
    left: 28%;
    transform: translate(-63%, 20%);
    height: 17%;
  }

  .pokemon__data {
    position: absolute;
    font-weight: 600;
    color: #aaa;
    top: 61%;
    right: 59%;
    font-size: clamp(8px, 5vw, 25px);
  }

  .pokemon__name {
    color: #3a444d;
    text-transform: capitalize;
  }

  .form {
    position: absolute;
    width: 30.5%;
    top: 70%;
    left: 14%;
  }

  .input__search {
    width: 100%;
    padding: 4%;
    outline: none;
    border: 2px solid #333;
    border-radius: 5px;
    font-weight: 600;
    color: #3a444d;
    background-color: #fff;
    font-size: clamp(8px, 5vw, 1rem);
    box-shadow: -3px 4px 0 #888, -5px 7px 0 #333;
  }

  .buttons {
    position: absolute;
    bottom: 10%;
    left: 30%;
    width: 27%;
    transform: translate(-57%, 0);
    display: flex;
    gap: 20px;
  }

  .button {
    width: 50%;
    padding: 4%;
    border: 2px solid #000;
    border-radius: 5px;
    font-size: clamp(8px, 5vw, 1rem);
    font-weight: 600;
    color: white;
    background-color: #23322F;
    box-shadow: -2px 3px 0 #222, -4px 6px #000;
  }

  .button:active {
    box-shadow: inset -4px 4px 0 #222;
    font-size: 0.9rem;
  }

  .stats-1-1 {
    position: absolute;
    bottom: 31%;
    left: 64%;
  }

  .badge-a{
    position: absolute;
    bottom: 12%;
    left: 60%;
  }

  .badge-b{
    position: absolute;
    bottom: 12%;
    left: 80%;
  }

</style>