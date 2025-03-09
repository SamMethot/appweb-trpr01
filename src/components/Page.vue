<script setup lang="ts">
import TopBar from './TopBar.vue'
import GameList from './GameList.vue'
import Modify from './Modify.vue'
import Details from './Details.vue'
import Add from './Add.vue'
import { type Game } from '../scripts/types'
import { ref, computed } from 'vue'

const games = ref<Game[]>([
    { id: 1, title: "caca", description: "caca", image: "/src/assets/img/noimage.png", rating: 5, genres: ["mmorpg", "survival"], releaseDate: "2/10/2024", price: 5,  stock: 1 },
    { id: 2, title: "poopoo", description: "www", image: "/src/assets/img/noimage.png", rating: 7, genres: ["adventure"], releaseDate: "8/1/2022", price: 10,  stock: 10 },
    { id: 3, title: "po po", description: "aaa", image: "/src/assets/img/noimage.png", rating: 3, genres: ["openworld"], releaseDate: "15/11/2021", price: 4,  stock: 5 }
])

const searchQuery = ref('')

const activeView = ref('list')

const currentGame = ref<Game>({
    id: 0, 
    title: '', 
    description: '', 
    image: '', 
    rating: 0, 
    genres: [], 
    releaseDate: '', 
    price: 0,
    stock: 0
})

const viewsToReturn = ['modify', 'preview', 'details']

const filteredGames = computed(() => {
    // Documentation du filter : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter
    return games.value.filter(game => 
        game.title.toLowerCase().includes(searchQuery.value.toLowerCase())
    )
})

function handleViewChange(view: string, game?: Game) {
    if (view === 'modify' && game) {
        currentGame.value = game
    }
    if (view === 'delete') {
        activeView.value = 'list'
        return
    }
    if (view === 'details' && game) {
        currentGame.value = game;
        activeView.value = 'details';
        return;
    }
    activeView.value = view
}

function handleGameUpdate(updatedGame: Game) {
    // Documentation du findIndex : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex
    const index = games.value.findIndex(game => game.id === updatedGame.id)
    if (index !== -1) {
        games.value[index] = updatedGame
    }
}

function handleSearch(query: string) {
    searchQuery.value = query
}

function handleDuplicateGame(newGame: Game) {
    games.value.push(newGame)
}

function handleDeleteGame(game: Game) {
    activeView.value = 'list'

    // Documentation du findIndex : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex
    const index = games.value.findIndex(gameItem => gameItem.id === game.id)

    if (index !== -1) {
        games.value.splice(index, 1)
    }
}
</script>

<template>
    <header class="header text-center bg-dark text-white p-3">
            <img v-if="viewsToReturn.includes(activeView)" src="../assets/img/arrow.png" alt="image de flèche" class="arrow" @click="handleViewChange('list')">  <!--- Image : https://www.flaticon.com/free-icon/arrow_507257?term=back+arrow&page=1&position=1&origin=tag&related_id=507257 ---->
            <img class="logo" src="../assets/img/Stime_logo.png" alt="Steam Logo">
        <h1>Stime Marketplace</h1>
    </header>

    <body class="body bg-dark p-3 min-vh-100">
        <div class="bg-dark p-3">
            <TopBar :games="games" @changeView="handleViewChange" @search="handleSearch" />
            
            <div v-if="activeView === 'list' && games.length > 0" class="games">
                <GameList v-if="games.length > 0" :game="currentGame" :games="filteredGames" @changeView="handleViewChange" @deleteGame="handleDeleteGame" @duplicateGame="handleDuplicateGame"/>            
            </div>
            <div v-if="activeView === 'list' && games.length === 0">
                <p class="text-white text-center mt-5">Aucun jeu à afficher</p>
            </div>

            <div v-if="activeView === 'add'">
                <Add :games="games"/>
            </div>

            <div v-if="activeView === 'delete'">
                <h2 class="text-white">Supprimer tous les jeux</h2>
                <p class="text-white">Êtes-vous sûr ?</p>
            </div>

            <div v-if="activeView === 'modify'">
                <Modify :game="currentGame" @update-game="handleGameUpdate" />
            </div>

            <div v-if="activeView === 'details'">
                <Details :game="currentGame"/>
            </div>
        </div>
    </body>
</template>

<style scoped>
</style>
