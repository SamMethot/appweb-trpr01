<script setup lang="ts">
import { type Game } from '../scripts/types'
import { ref } from 'vue'
import AddPreview from './AddPreview.vue'
import Modal from 'bootstrap/js/dist/modal'

const props = defineProps<{ game: Game }>()

const emit = defineEmits(['updateGame'])

const currentGame = ref<Game>({ ...props.game })

const Errors = ref<string[]>([])

function modifyGame() {
    Errors.value = [] 
    verifyGame()
    if (Errors.value.length === 0) {
        emit('updateGame', currentGame.value)
        showSuccessModal()
    }
}

const genresList = ref([
    { label: 'MMORPG', value: 'mmorpg' },
    { label: 'Survie', value: 'survival' },
    { label: 'Aventure', value: 'adventure' },
    { label: 'Monde Ouvert', value: 'openworld' }
])

function verifyGame() {
    if (currentGame.value.title.trim() === '') {
        Errors.value.push('Title is required')
    }
    if (currentGame.value.description.trim() === '') {
        Errors.value.push('Description is required')
    }
    if (currentGame.value.image.trim() === '') {
        Errors.value.push('Image is required')
    }
    if (currentGame.value.rating === 0 || currentGame.value.rating > 10 || currentGame.value.rating < 0) {
        Errors.value.push('Rating is required')
    }
    if (currentGame.value.genres.length === 0) {
        Errors.value.push('Genres are required')
    }
    if (currentGame.value.releaseDate.trim() === '') {
        Errors.value.push('Release Date is required')
    }
    if (currentGame.value.price === 0 || currentGame.value.price < 0) {
        Errors.value.push('Price is required')
    }
    if (currentGame.value.stock < 0) {
        Errors.value.push('Stock is required')
    }
}

function showSuccessModal() {
    const modalElement = document.getElementById('successModal');
    if (modalElement) {
        const modal = new Modal(modalElement);
        modal.show();
    }
}
</script>

<template>
    <!-- Documentation du modal : https://bootstrap-vue.org/docs/components/modal -->
    <div class="modal fade" id="successModal" tabindex="-1" aria-labelledby="successModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content bg-success text-white">
                <div class="modal-header">
                    <h5 class="modal-title" id="successModalLabel">Succès</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Fermer"></button>
                </div>
                <div class="modal-body">
                    Les modifications ont été effectuées avec succès !
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-danger " data-bs-dismiss="modal">Fermer</button>
                </div>
            </div>
        </div>
    </div>

    <div class="game-details-container d-flex justify-content-center gap-3">
        <div class="card bg-dark mt-5 text-white p-3 rounded-3">
            <h1>Modifier un jeu</h1>

            <div class="pb-3">
                <label for="title">Titre: </label>
                <input type="text" id="title" v-model="currentGame.title" class="form-control" placeholder="Title" />
                <div v-if="Errors.includes('Title is required')" class="text-danger">
                    Le titre est invalide.
                </div>
            </div>

            <div class="w-100 pb-3">
                <label for="desc">Description: </label>
                <textarea name="description" id="desc" v-model="currentGame.description" class="form-control" placeholder="Description"></textarea>
                <div v-if="Errors.includes('Description is required')" class="text-danger">
                    La description est invalide.
                </div>
            </div>

            <div class="pb-3">
                <label for="image">Image (utilisez une URL valide): </label>
                <input type="text" id="image" class="form-control" v-model="currentGame.image" placeholder="Image URL" />
                <div v-if="Errors.includes('Image is required')" class="text-danger">
                    L'image est invalide.
                </div>
            </div>

            <div class="pb-3">
                <label for="rating">Évaluation (1-10): </label>
                <input type="number" id="rating" class="form-control" v-model="currentGame.rating" value="1" />
                <div v-if="Errors.includes('Rating is required')" class="text-danger">
                    L'évaluation est requis.
                </div>
            </div>

            <div class="pb-3">
                <label>Genres: </label>
                <div class="d-flex flex-wrap">
                    <div v-for="genre in genresList" :key="genre.value" class="form-check me-3">
                        <input type="checkbox" class="form-check-input" :id="genre.value" :value="genre.value" v-model="currentGame.genres" />
                        <label class="form-check-label" :for="genre.value">{{ genre.label }}</label>
                    </div>
                </div>
                <div v-if="Errors.includes('Genres are required')" class="text-danger">
                    Le(s) genre(s) est(sont) invalide(s).
                </div>
            </div>

            <div class="pb-3">
                <label for="releaseDate">Release Date: </label>
                <input type="date" id="releaseDate" class="form-control" v-model="currentGame.releaseDate" />
                <div v-if="Errors.includes('Release Date is required')" class="text-danger">
                    La date de publication est invalide.
                </div>
            </div>

            <div class="pb-3">
                <label for="price">Price: </label>
                <input type="number" id="price" class="form-control" v-model="currentGame.price" />
                <div v-if="Errors.includes('Price is required')" class="text-danger">
                    Le prix est invalide.
                </div>
            </div>

            <div class="pb-3">
                <label for="stock">Quantité: </label>
                <input type="number" id="stock" class="form-control" v-model="currentGame.stock" />
                <div v-if="Errors.includes('Stock is required')" class="text-danger">
                    La quantité est invalide.
                </div>
            </div>

            <button class="btn btn-success" @click="modifyGame()">Appliquer les modifications</button>
        </div>

        <div class="card bg-dark text-white mt-5 rounded-3">
            <AddPreview :newGame="currentGame" />
        </div>
    </div>
</template>

<style scoped>
.form-control {
    background-color: #2c2f38;
    border: 1px solid #444;
    color: #fff;
    border-radius: 8px;
    font-size: 1rem;
}

.card-body {
    padding: 20px;
}

.game-image {
    max-width: 100%;
    height: auto;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.game-details-container {
    width: 80%;
    margin: 0 auto;
    padding: 20px;
    background-color: #121212;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    color: #fff;
}

.card {
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    min-height: 100%;
    width: 400px;
}

h1 {
    font-size: 2.5rem;
    color: #fff;
}

.text-danger {
    font-size: 0.9rem;
    margin-top: 5px;
}

.btn-success {
    background-color: #28a745;
    border: none;
}

.form-control {
    background-color: #2c2f38;
    border: 1px solid #444;
    color: #fff;
    border-radius: 8px;
    font-size: 1rem;
}

.form-check-label {
    color: #fff;
}

.form-check-input {
    background-color: #2c2f38;
    border: 1px solid #444;
    color: #fff;
}

.full-height {
    height: 100%;
}
</style>