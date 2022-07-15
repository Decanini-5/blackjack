<template>
    <div class="pop-up">
        <p>Your cards sum: {{ cardSum }}</p>
        <p v-if="showDealerSum"> The dealer's cards sum: {{ dSum }} </p>
        <p>{{ text }}</p>
        <button class="replay-button" @click="$emit('replay')">PLAY AGAIN</button>
    </div>
</template>

<script>
export default {
    name: "ResPopUp",
    props: {
        cardSum: {type: Number, required: true},
        text: {type: String, required: true},
        dSum: {type: Number},
        showDealerSum: {type: Boolean},
    }
}
</script>

<style>
.pop-up {
    background-color: rgba(105, 105, 105);
    color: whitesmoke;
    border-radius: 7px;
    position: absolute;
    width: 400px;
    padding: 0.2rem 2rem 1rem;
    z-index: 999;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    white-space: pre;
}
.replay-button {
    position: relative;
    margin: 10px 0 5px;
}
</style>