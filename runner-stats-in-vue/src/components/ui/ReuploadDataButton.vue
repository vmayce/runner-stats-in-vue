<template>
    <button class="button" v-on:click="emitGlobalClickEvent">
        <slot>Button</slot>
    </button>
</template>


<script>
    import { EventBus } from './../../eventbus.js';

    export default {
        name: 'ReuploadDataButton',
        methods: {
            emitGlobalClickEvent() {
                EventBus.$emit('reupload-data-clicked', true);

            }
        },
    }
</script>


<style scoped>


</style>
