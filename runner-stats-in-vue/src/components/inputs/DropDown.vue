


<template>
    <select v-model="selected" @change="emitGlobalClickEvent">
        <option v-for="(opt, idx) in options" :key="idx" v-bind:value="{ id: opt.id, text: opt.text }">
            {{opt.text}}
        </option>
    </select>

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
    select {
        /* remove the tiny arrow */
        -webkit-box-sizing: border-box;
        -moz-box-sizing: border-box;
        box-sizing: border-box;
        -webkit-appearance: none;
        -moz-appearance: none;

    }

        select:active,
        select:focus {
            border-top-left-radius: 17px;
            border-top-right-radius: 17px;
            border-bottom-left-radius: 0;
            border-bottom-right-radius: 0;
        }

    /* update the tiny arrows */
    select {
        background-image: linear-gradient(45deg, transparent 50%, #DE9E36 50%), linear-gradient(135deg, #DE9E36 50%, transparent 50%);
        background-position: calc(100% - 13px) calc(1em + 2px), calc(100% - 8px) calc(1em + 2px), 100% 0;
        background-size: 5px 5px, 5px 5px, 2.5em 2.5em;
        background-repeat: no-repeat;
    }

        select:focus {
            background-image: linear-gradient(45deg, white 50%, transparent 50%), linear-gradient(135deg, transparent 50%, white 50%);
            background-position: calc(100% - 8px) 1em, calc(100% - 13px) 1em, 100% 0;
            background-size: 5px 5px, 5px 5px, 2.5em 2.5em;
            background-repeat: no-repeat;
            border-color: grey;
            outline: 0;
        }
</style>
