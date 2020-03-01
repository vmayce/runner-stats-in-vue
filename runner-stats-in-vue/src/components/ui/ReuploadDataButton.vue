<template>
    <button class="Button" v-on:click="emitGlobalClickEvent">
        <slot>Button</slot>
    </button>
</template>


<script>
    import { EventBus } from './../../eventbus.js';

    export default {
        name: 'ReuploadDataButton',
        methods: {
            emitGlobalClickEvent() {
                EventBus.$emit('show-uploaded-data', true);

            }
        },
    }
</script>


<style scoped>
</style>
