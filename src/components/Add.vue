<script setup lang="ts">
import { type Game } from '../scripts/types'
import { ref, watchEffect } from 'vue'
import AddPreview from './AddPreview.vue'
import errorImage from '/src/assets/img/noimage.png'
import Modal from 'bootstrap/js/dist/modal.js'

const props = defineProps<{ 
    games: Game[]
}>()

const newGame = ref<Game>({
    id: 0,
    title: '',
    description: '',
    image: '',
    rating: 1,
    genres: [],
    releaseDate: '',
    price: 0,
    stock: 0
})

const Errors = ref<string[]>([])

const validImage = ref<string>(errorImage)

async function checkImage(url: string) {
    if (!url || url.trim() === '') {
        validImage.value = errorImage;
        return;
    }

    const img = new Image();
    img.src = url;
    img.onload = () => validImage.value = url;
    img.onerror = () => validImage.value = errorImage;
}

watchEffect(() => {
    checkImage(newGame.value.image);
})

function addGame() {
    Errors.value = [] 

    verifyGame()
    if (Errors.value.length === 0) {
        newGame.value.image = validImage.value;
        props.games.push(newGame.value)

        const modalElement = document.getElementById('successModal');
        if (modalElement) {
            const modal = new Modal(modalElement);
            modal.show();
        }
    }
}

const genresList = ref([
    { label: 'MMORPG', value: 'mmorpg' },
    { label: 'Survie', value: 'survival' },
    { label: 'Aventure', value: 'adventure' },
    { label: 'Monde Ouvert', value: 'openworld' }
])

function verifyGame()
{
    if (newGame.value.title.trim() === '') {
        Errors.value.push('Title is required')
    }
    if (newGame.value.description.trim() === '') {
        Errors.value.push('Description is required')
    }
    if (newGame.value.image.trim() === '') {
        Errors.value.push('Image is required')
    }
    if (newGame.value.rating === 0 || newGame.value.rating > 10 || newGame.value.rating < 0) {
        Errors.value.push('Rating is required')
    }
    if (newGame.value.genres.length === 0) {
        Errors.value.push('Genres are required')
    }
    if (newGame.value.releaseDate.trim() === '') {
        Errors.value.push('Release Date is required')
    }
    if (newGame.value.price === 0 || newGame.value.price < 0) {
        Errors.value.push('Price is required')
    }
    if (newGame.value.stock < 0) {
        Errors.value.push('Stock is required')
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
                    Le jeu a été ajouté avec succès!
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-danger " data-bs-dismiss="modal">Fermer</button>
                </div>
            </div>
        </div>
    </div>

    <div class="game-details-container d-flex justify-content-center gap-3">
        <div class="card bg-dark text-white mt-5">
            <div class="card-body">
                <h1>Ajouter un jeu</h1>
                <div class="pb-3">
                    <label for="title">Titre</label>
                    <input type="text" id="title" v-model="newGame.title" class="form-control" />
                    <div v-if="Errors.includes('Title is required')" class="text-danger">
                        Le titre est invalide.
                    </div>
                </div>

                <div class="w-100 pb-3">
                    <label for="desc">Description</label>
                    <textarea name="description" id="desc" v-model="newGame.description" class="form-control"></textarea>
                    <div v-if="Errors.includes('Description is required')" class="text-danger">
                        La description est invalide.
                    </div>
                </div>
                
                <div class="pb-3">
                    <label for="image">Image (utilisez une URL valide)</label>
                    <input type="text" id="image" class="form-control" v-model="newGame.image"/>
                    <div v-if="Errors.includes('Image is required')" class="text-danger">
                        L'image est invalide.
                    </div>
                </div>

                <div class="pb-3">
                    <label for="rating">Évaluation (1-10)</label>
                    <input type="number" id="rating" class="form-control" v-model="newGame.rating" value="1" />
                    <div v-if="Errors.includes('Rating is required')" class="text-danger">
                        L'évaluation est requise.
                    </div>
                </div>

                <div class="pb-3">
                    <label>Genres: </label>
                    <div class="d-flex flex-wrap">
                        <div v-for="genre in genresList" :key="genre.value" class="form-check me-3">
                            <input type="checkbox" class="form-check-input" :id="genre.value" :value="genre.value" v-model="newGame.genres" />
                            <label class="form-check-label" :for="genre.value">{{ genre.label }}</label>
                        </div>
                    </div>
                    <div v-if="Errors.includes('Genres are required')" class="text-danger">
                        Le(s) genre(s) est(sont) invalide(s).
                    </div>
                </div>

                <div class="pb-3">
                    <label for="releaseDate">Release Date</label>
                    <input type="date" id="releaseDate" class="form-control" v-model="newGame.releaseDate" />
                    <div v-if="Errors.includes('Release Date is required')" class="text-danger">
                        La date de publication est invalide.
                    </div>
                </div>

                <div class="pb-3">
                    <label for="price">Price</label>
                    <input type="number" id="price" class="form-control" v-model="newGame.price" />
                    <div v-if="Errors.includes('Price is required')" class="text-danger">
                        Le prix est invalide.
                    </div>
                </div>

                <div class="pb-3">
                    <label for="stock">Quantité</label>
                    <input type="number" id="stock" class="form-control" v-model="newGame.stock" />
                    <div v-if="Errors.includes('Stock is required')" class="text-danger">
                        La quantité est invalide.
                    </div>
                </div>

                <button class="btn btn-success" @click="addGame()">Ajouter le jeu</button>
            </div>
        </div>

        <div class="card bg-dark text-white mt-5">
            <AddPreview :newGame="newGame" />
        </div>
    </div>
</template>

<style scoped>
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

.pb-3 {
    padding-bottom: 1rem;
}
</style>