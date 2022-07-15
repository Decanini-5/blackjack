<template>
  <div class="deck-card"
    :class="[getColor(suit)]"
  >
    <div class="suit">{{ getSuit(suit) }}</div>
    <div class="rank">{{ getRank(rank) }}</div>
  </div>
</template>

<script>
export default {
    name: "DeckCard",
    props: {
        suit: {type: String, required: true},
        rank: {type: String, required: true}
    },
    methods: {
        getColor(suit) {
            return suit==='Hearts' || suit==='Diamonds' ? 'red' : 'black'
        },
        getSuit(suit) {
            switch(suit) {
                case 'Hearts':
                    return '♥'
                case 'Diamonds':
                    return '♦'
                case 'Clubs':
                    return '♣'
                case 'Spades':
                    return '♠'
            }
        },
        getRank(rank) {
            switch(rank) {
                case 'Ace':
                    return 'A'
                case 'Jack':
                    return 'J'
                case 'Queen':
                    return 'Q'
                case 'King':
                    return 'K'
                default:
                    return rank
            }
        }
    },
}
</script>

<style scoped>
    .deck-card {
        border: 1px darkslategray solid;
        border-radius: 7px;
        width: 70px;
        height: 100px;
        position: relative;
        display: flex;
        justify-content: center;
        align-items: center;
        margin: 0.5em;
        background-color: whitesmoke;
    }
    .red {
        color: red;
        font-weight: bold;
    }
    .black {
        color: black;
        font-weight: bold;
    }
    .rank {
        top: 0.2em;
        right: 0.4em;
        position: absolute;
    }
    .suit {
        font-size: 380%;
    }
</style>
