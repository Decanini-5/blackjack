<template>
    <div class="pop-up">
        <p>{{ text }}<br />Enter your betting amount for this round: </p>
        <input
            class="bet-input"
            type="number"
            placeholder="0">
        <button class="bet-button" @click="$emit('betted')">Begin Round!</button>
    </div>
</template>

<script>
export default {
    name: "BetPopUp",
    props: {
        text: {type: String, required: true}
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
}
.bet-input {
    padding: 4px;
    width: 30px;
}
.bet-button {
    display: block;
    margin: 20px 0 5px;
}
</style>