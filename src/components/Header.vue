<script setup>

</script>


<template>
    <span>
        <h1 class="header dm-serif">
            <slot>Text</slot>
        </h1>
    </span>
</template>


<style scoped>

.header {
    position: relative;

    width: max-content;

    font-size: 6vw;
    text-align: center;
    text-transform: capitalize;
}

/* #header::after {
    position: absolute;

    content: 'Where Do You Want To Go Next?';

    top: 50%;
    left: 0%;
    width: 100%;

    filter: blur(2px);

    background: -webkit-linear-gradient(rgba(0, 0, 0, 0), rgba(255, 255, 255, 0.231));
    background-clip: text;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;

    text-align: center;

    transform: scaleY(-1);
} */

</style>