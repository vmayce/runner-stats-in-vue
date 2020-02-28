


<template>
    <div class="input">
        <select v-model="selected" @change="emitGlobalClickEvent">
            <option v-for="(opt, idx) in options" :key="idx" v-bind:value="{ id: opt.id, text: opt.text }">
                {{opt.text}}
            </option>
        </select>

    </div>
</template>

<script>
    import { EventBus } from './../../eventbus.js';

    export default {
        name: 'DropDown',
        props: {
            options: Array,
            selectedOption: Object,
            emitEventName: String
        },
        data: function () {
            return {
                selected: null
            }
        },
        methods: {
            emitGlobalClickEvent() {
                EventBus.$emit(this.emitEventName, this.selected);
            }
        },
        mounted: function () {
            this.selected = this.selectedOption;
        }
    }

</script>


<style scoped>
    input {
        font-size: 2em;
    }
</style>
