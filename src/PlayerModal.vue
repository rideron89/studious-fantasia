<template>
    <div class="modal">
        <div class="modal--background" @click="close"></div>

        <div class="modal--content">
            <div class="modal--close" @click="close">&cross;</div>

            <h2>Player Details: {{ data.Name }}</h2>

            <hr />

            <p>Put some player info here...</p>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'player-modal',

        props: ['data'],

        methods: {
            close: function() {
                this.$emit('close')
            }
        }
    }
</script>

<style>
    .modal {
        height: 100vh;
        left: 0;
        position: fixed;
        top: 0;
        width: 100vw;
        z-index: 1;
    }

    .modal--background {
        background: rgba(0, 0, 0, 0.5);
        height: 100%;
        left: 0;
        position: absolute;
        top: 0;
        width: 100%;
        z-index: 1;
    }

    .modal--content {
        background: #ffffff;
        border-radius: 2px;
        height: calc(100vh - 80px);
        left: 0;
        overflow: auto;
        margin: 0 auto;
        max-width: 640px;
        padding: 40px;
        position: relative;
        right: 0;
        top: 40px;
        width: calc(100% - 80px);
        z-index: 2;
    }

    .modal--close {
        cursor: pointer;
        font-weight: 700;
        padding: 2px 8px;
        position: absolute;
        right: 20px;
        top: 15px;
    }
</style>