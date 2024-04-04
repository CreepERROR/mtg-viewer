<script>
import { fetchAllCards } from '../services/cardService';

export default {
    data() {
        return {
            loadingCards: true,
            cards: [],
            word: '',
            recherche: [],
            split: [],
            page: 0,
            max: 0,
        };
    },
    async mounted() {
        this.cards = await fetchAllCards();
        this.max = Math.ceil(this.cards.length / 30);
        this.page = 1;
        this.loadingCards = false;
        this.recherche = this.cards;
        this.split = this.cards.slice(0, 30);
    },
    methods: {
        nextpage() {
            if (!(this.page >= this.max)) {
                this.page += 1;
            }
            console.log(`${this.page} / ${this.max}`);
            this.split = this.recherche.slice((this.page - 1) * 30, (this.page) * 30);
        },
        prevpage() {
            if (this.page > 1) {
                this.page -= 1;
            }
            console.log(`${this.page} / ${this.max}`);
            this.split = this.recherche.slice((this.page - 1) * 30, (this.page) * 30);
        },
        submit() {
            console.log(this.word);
            console.log(this.cards.length);
            this.recherche = this.cards.filter((obj) => obj.name.includes(this.word));
            console.log(this.recherche.length);
            this.split = this.recherche.slice(0, 30);
            this.page = 1;
            this.max = Math.ceil(this.recherche.length / 30);
        },
    },
};
</script>

<template>
    <div>
        <h1>Rechercher une Carte</h1>
    </div>
    <div class="card-list">
        <div v-if="loadingCards">Loading...</div>
        <div v-else>
            <input type="text" v-model="word" placeholder="Carte ici">
            <button @click="submit">Rechercher</button>
            <div>
                <button @click="nextpage">Page suivante</button>
                <button @click="prevpage">Page précédente</button>
                <p>page : {{this.page}} sur {{this.max}}</p>
            </div>
            <div class="card" v-for="card in split" :key="card.id">
                <router-link :to="{ name: 'get-card', params: { uuid: card.uuid } }"> {{ card.name }} - {{
                    card.uuid
                }}
                </router-link>
            </div>
        </div>
    </div>
</template>
