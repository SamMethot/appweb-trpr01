<script setup lang="ts">
import { defineEmits, ref } from 'vue';
import { type Game } from '../scripts/types';
import VueJsonCsv from 'vue-json-csv'


const emit = defineEmits(['changeView', 'search'])
const searchQuery = ref('')

const props = defineProps<{ games: Game[] }>()

function searchGame() {
    emit('search', searchQuery.value)
}


</script>

<template>
    <div class="container pb-3">
        <div class="row">
            <div class="col-md-6">
                <div class="position-relative w-100">
                    <input type="text" class="form-control input-with-icon" placeholder="Entrez le titre du jeu ici..." v-model="searchQuery" @input="searchGame"/>                
                </div>
            </div>
            <div class="col-md-6">
                <div class="d-grid gap-3 grid-buttons">
                    <button class="btn btn-primary" @click="emit('changeView', 'list')">Liste des jeux</button>
                    <button class="btn btn-success" @click="emit('changeView', 'add')">Ajouter</button>

                    <!-- Documentation : https://www.npmjs.com/package/vue-json-csv -->
                    <vue-json-csv 
                        :data="props.games" 
                        :fields="[ 'id', 'title', 'description', 'image', 'rating', 'genres', 'releaseDate', 'price', 'stock']" 
                        name="games_export" 
                        class="btn btn-warning">
                        Exporter les jeux en CSV
                    </vue-json-csv>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.input-icon {
    right: 10px;
    top: 50%;
    width: 20px;
}

.grid-buttons {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
}
</style>