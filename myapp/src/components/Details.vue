<script setup lang="ts">
import { defineProps, computed } from 'vue';
import { type Game } from '../scripts/types';

const props = defineProps<{ game: Game }>();


const formattedGenres = computed(() => {
    return props.game.genres.join(', ');
});

</script>
<template>
    <div class="game-details-container">
        <div class="game-header text-white">
            <h1>{{ props.game.title }}</h1>
            <p class="text-success price">{{ props.game.price }}$</p>
        </div>

        <div class="game-image">
            <img :src="props.game.image || '/src/assets/img/noimage.png'" :alt="`Image du jeu ${props.game.title}`" class="game-image-content" />
        </div>

        <div class="game-info">
            <p><strong>Description:</strong> {{ props.game.description }}</p>
            <p><strong>Genres:</strong> {{ formattedGenres }}</p>
            <p><strong>Date de publication:</strong> {{ props.game.releaseDate }}</p>
            <p><strong>Évaluation:</strong> {{ props.game.rating }} / 10</p>
            <p><strong>Quantité:</strong> {{ props.game.stock }}</p>
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

.game-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.game-header h1 {
    font-size: 2.5rem;
    color: #fff;
}

.price {
    font-size: 1.5rem;
    font-weight: bold;
}

.game-image {
    text-align: center;
    margin-bottom: 20px;
}

.game-image-content {
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
