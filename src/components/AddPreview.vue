<script setup lang="ts">
import { type Game } from '../scripts/types'
import { defineProps, ref, watchEffect } from 'vue'
import errorImage from '/src/assets/img/noimage.png'

const props = defineProps<{ 
    newGame: Game
}>()

const validImage = ref<string>(errorImage)

async function checkImage(url: string) { 
    if (!url || url.trim() === '') {
        validImage.value = errorImage
        return
    }

    const img = new Image()
    img.src = url
    img.onload = () => validImage.value = url
    img.onerror = () => validImage.value = errorImage
}

watchEffect(() => {
    checkImage(props.newGame.image)
})
</script>

<template>
    <div class="game-details-container bg-dark text-white p-4">
        <div class="game-header">
            <h1>{{ props.newGame.title || "Aucun titre" }}</h1>
            <p class="text-success price">{{ props.newGame.price || 0 }}$</p>
        </div>

        <div class="game-image-container text-center">
            <img :src="validImage" :alt="`Image du jeu`" class="game-image" />
        </div>

        <div class="game-info">
            <p><strong>Description:</strong> {{ props.newGame.description || "Aucune Information" }}</p>
            <p><strong>Genres:</strong> {{ props.newGame.genres.join(', ') || "Aucune Information" }}</p>
            <p><strong>Date de sortie:</strong> {{ props.newGame.releaseDate || "Aucune Information" }}</p>
            <p><strong>Évaluation:</strong> {{ props.newGame.rating || 0 }} / 10</p>
            <p><strong>Quantité:</strong> {{ props.newGame.stock || 0 }}</p>
        </div>
    </div>
</template>

<style scoped>
.game-details-container {
    width: 100%;
    border-radius: 8px;
    padding: 20px;
}

.game-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.game-header h1 {
    font-size: 2rem;
}

.price {
    font-size: 1.5rem;
    font-weight: bold;
    margin: 0;
}

.game-image-container {
    text-align: center;
    margin-bottom: 20px;
}

.game-image {
    max-width: 100%;
    height: auto;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.game-info {
    font-size: 1.2rem;
}

.game-info p {
    margin-bottom: 10px;
}

.game-info strong {
    color: #fff;
}
</style>