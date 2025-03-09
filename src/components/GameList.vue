<script setup lang="ts">
import { type Game } from '../scripts/types'
import { defineProps, ref, defineEmits, onMounted } from 'vue'
import Modal from 'bootstrap/js/dist/modal.js'

const emit = defineEmits(['changeView', 'duplicateGame', 'deleteGame'])

const props = defineProps<{ games: Game[] }>()

const selectedGame = ref<Game | null>(null)

const outOfStockGame = ref<Game | null>(null)

function openDeleteModal(game: Game) {
    selectedGame.value = game;
    const modal = new Modal(document.getElementById('deleteModal')!);
    modal.show();
}

function confirmDelete() {
    if (selectedGame.value) {
        emit('deleteGame', selectedGame.value)
    }
    const modal = Modal.getInstance(document.getElementById('deleteModal')!);
    if (modal) {
        modal?.hide();
    }
}

function duplicateGame(game: Game) {
    const newGame = { ...game, id: game.id + props.games.length }
    emit('duplicateGame', newGame)  
}

function openOutOfStockModal(game: Game) {
    outOfStockGame.value = game;
    const modal = new Modal(document.getElementById('outOfStockModal')!);
    modal.show();
}

onMounted(() => {
    props.games.forEach(game => {
        if (game.stock === 0) {
            openOutOfStockModal(game);
        }
    });
});
</script>

<template>
    <!-- Documentation du modal : https://bootstrap-vue.org/docs/components/modal -->
    <div class="modal fade" id="deleteModal" tabindex="-1" aria-labelledby="deleteModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content bg-secondary text-white">
                <div class="modal-header">
                    <h5 class="modal-title" id="deleteModalLabel">Confirmation</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Fermer"></button>
                </div>
                <div class="modal-body">
                    Êtes-vous sûr de vouloir supprimer <strong>{{ selectedGame?.title }}</strong> ?
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Annuler</button>
                    <button type="button" class="btn btn-danger" @click="confirmDelete">Oui, Supprimer</button>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="outOfStockModal" tabindex="-1" aria-labelledby="outOfStockModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content bg-warning text-dark">
                <div class="modal-header">
                    <h5 class="modal-title" id="outOfStockModalLabel">Rupture de stock</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Fermer"></button>
                </div>
                <div class="modal-body">
                    Le jeu <strong>{{ outOfStockGame?.title }}</strong> est en rupture de stock.
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Fermer</button>
                </div>
            </div>
        </div>
    </div>

    <div class="games-container">
        <div v-for="game in props.games" :key="game.id" class="game-item">
            <h2>{{ game.title }}</h2>
            <div class="game-image-container">
                <img :src="game.image" :alt="`Image du jeu ${game.title}`" class="game-image" />
            </div>
            <div class="game-info">
                <div class="game-price-stock">
                    <!-- Documentation du changement de classe sur la style : https://vuejs.org/guide/essentials/class-and-style -->
                    <div :class="{ 'text-success': game.stock > 5, 'text-warning': game.stock > 0 && game.stock <= 5, 'text-danger': game.stock === 0}" class="stock">
                        <span v-if="game.stock > 0">Quantité: {{ game.stock }}</span>
                        <span v-else>Rupture de stock</span>
                    </div>
                    <p class="price">{{ game.price }}$</p>
                </div>
            </div>
            <div class="d-flex justify-content-around">
                <button class="btn btn-danger" @click="openDeleteModal(game)">Supprimer</button>
                <button class="btn btn-warning" @click="emit('changeView', 'modify', game)">Modifier</button>
                <button class="btn btn-primary" @click="emit('changeView', 'details', game)">Détails</button>
                <button class="btn btn-success" @click="duplicateGame(game)">Dupliquer</button>
            </div>
        </div>
    </div>
</template>

<style scoped>
.games-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    margin: 10px;
}

.game-item {
    position: relative;
    background-color: #121212;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);   
    color: white;
    width: 30%;
    margin: 10px;
    padding: 10px;
    text-align: center;
    overflow: hidden;
}

.game-image-container {
    position: relative;
    width: 100%;
    height: 200px;
}

.game-image {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.game-info {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 10px;
}


.game-price-stock {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    margin-bottom: 10px;
    text-align: left;
}

.stock, .price {
    margin: 0;
    font-size: 16px;
    font-weight: bold;
}

.stock {
    display: inline-flex;
}

.price {
    color: green;
    text-align: right;
}

h2 {
    margin-top: 10px;
    font-size: 18px;
}
</style>