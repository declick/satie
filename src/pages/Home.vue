<template>
  <div class="home-page">
    <h1 class="title">Carnet d'un héro</h1>
    <p class="subtitle">Plonge dans l'aventure où tu es le héros. Découvre les règles et prépare-toi à jouer.</p>
    <button 
      @click="$emit('navigate', 'perso')" 
      class="start-button" 
      :disabled="adventureStarted"
    >
      Commencer l'aventure
    </button>
    <button 
      @click="$emit('navigate', 'regle')" 
      class="regle-button" 
      :disabled="adventureStarted"
    >
      Modifier les regle
    </button>
    <div class="illustration">
      <img src="https://www.vox-animae.com/wp-content/uploads/2023/10/Comportement-lapin.png" alt="Illustration du jeu" />
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
  props: {
    adventureStarted: {
      type: Boolean,
      default: false
    }
  }
};

</script>

<style scoped>
.home-page {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  font-family: 'Arial', sans-serif;
  padding: 20px;
}

.title {
  font-size: 3rem;
  font-weight: bold;
  margin-bottom: 1rem;
}

.subtitle {
  font-size: 1.5rem;
  margin-bottom: 2rem;
}

.illustration img {
  max-width: 100%;
  border: 2px solid black;
  border-radius: 5px;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>