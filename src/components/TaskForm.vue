<script setup lang="ts">
import { ref, reactive, computed, onMounted, watch } from 'vue';

const categories = ref(["sport", "travail", "loisir", "autres" ])
const statusList = ref(["fait", "pas fait", "en cours"])

interface Data {
    id: number
    name: string
    status: string
    dure: number
    categorie: string
    description: string
}
const form = reactive({
    id: 1,
    name: '',
    status: '',
    dure: 0,
    categorie: '',
    description: ''
})


const database = ref<Data[]>([])

onMounted(() => {
  const saved = localStorage.getItem('etiquette');
  if (saved) {
    // On fusionne pour éviter de perdre des clés si le code a changé
    Object.assign(database.value, JSON.parse(saved));
  }
});

// persistance de donnée
watch(
  () => database.value, 
  (form) => {
    localStorage.setItem('etiquette', JSON.stringify(form));
    console.log('Brouillon sauvegardé !');
  }, 
  { deep: true }
);

const sent = () => {
    database.value.push({
        id:form.id,
        name: form.name,
        status: form.status,
        dure: form.dure,
        categorie: form.categorie,
        description: form.description
    })
    form.id = form.id + 1
    form.name = ''
    form.status = " "
    form.dure = 0
    form.categorie = ''
    form.description = ''
}


</script>

<template>

<div class="app-container">
    <h1>Liste d'activité</h1>
        <div >
            <input  placeholder="Recherche" />
        </div><hr>
    
    <form @submit.prevent="sent" class="activity-form">
        <div class="field">
            <label>Titre de la Tâche</label>
            <input v-model="form.name" placeholder="Ex: Musculation" required/>
        </div>

        <!-- Champ status -->
        <label >Status</label>
        <select v-model="form.status" class="field">
            <option v-for="s in statusList">{{ s}}</option>
        </select>

        <label >Catégorie</label>
        <select v-model="form.categorie" class="field">
            <option v-for="categorie in categories">{{ categorie}}</option>
        </select>
         <!-- champ description -->

        <div class="field">
            <label>Description</label>
            <textarea v-model="form.description" placeholder="Decrire la tâche ..."></textarea>
        </div>

        <!-- button -->
        <button type="submit">Enregistrer la Tâche</button>
    </form>

    <div class="preview-card">
        <h3>Récapitulatif en direct :</h3>
        <p><strong>Nom :</strong> {{ form.name }}</p>
        <p><strong>Durée :</strong> {{ form.dure }} min</p>
        <p><strong>Catégorie :</strong> {{ form.categorie }}</p>
    </div>
    <div class="preview-card">
        <h3>Taches en cours :</h3>
        <p v-for="data in database">{{ data }}</p>
    </div>
  </div>

</template>

<style scoped>

/* Conteneur principal */
.app-container {
  font-family: 'Inter', sans-serif;
  max-width: 450px;
  margin: 40px auto;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 10px 25px rgba(0,0,0,0.1);
}

h1 {
  color: #2c3e50;
  text-align: center;
  font-size: 1.5rem;
  margin-bottom: 25px;
}

/* Style du formulaire */
.activity-form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 5px;

}

label {
  font-size: 0.85rem;
  font-weight: 600;
  color: #64748b;
}

input, select, textarea {
  padding: 10px 12px;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 1rem;
  outline: none;
  transition: border-color 0.2s;
}

input:focus, select:focus {
  border-color: #42b883; /* Vert Vue.js */
}

/* Bouton */
button {
  background-color: #42b883;
  color: white;
  border: none;
  padding: 12px;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
  margin-top: 10px;
  transition: background 0.3s;
}

button:hover {
  background-color: #33a06f;
}

/* Carte de prévisualisation en bas */
.preview-card {
  margin-top: 30px;
  padding: 15px;
  background-color: #f8fafc;
  border-left: 4px solid #42b883;
  border-radius: 4px;
}

.preview-card h3 {
  margin-top: 0;
  font-size: 0.9rem;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.preview-card p {
  margin: 8px 0;
  color: #334155;
}

</style>
