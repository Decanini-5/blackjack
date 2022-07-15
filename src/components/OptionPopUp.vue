<template>
    <div class="pop-up">
        <p>Your cards sum: {{ cardSum }} <br />Do you want to receive another card or stay?</p>
        <button class="hit-button" @click="$emit('hit')">HIT</button>
        <button class="stay-button" @click="$emit('stay')">STAY</button>
    </div>
</template>

<script>
export default {
    name: "OptionPopUp",
    props: {
        cardSum: {type: Number, required: true}
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
.hit-button {
    position: relative;
    margin: 10px 0 5px;
}
.stay-button {
    position: relative;
    margin: 10px 20px 5px;
}
</style>