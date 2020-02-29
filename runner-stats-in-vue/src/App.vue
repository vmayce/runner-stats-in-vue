<template>
    <div id="app">
        <NavBar :years="years" />
        <FileUpload @updateFileUpload="updateRunningData" />
        <CalculatedDistance :fileData="runningData" :jsonFileName="distanceJsonFile" :yearFilter="selectedYear" :monthFilter="selectedMonth" />

    </div>
</template>

<script>
    import NavBar from './components/navigation/NavBar.vue'
    import FileUpload from './components/inputs/FileUpload.vue'
    import CalculatedDistance from './components/CalculatedDistance.vue'
    import { EventBus } from './eventbus.js';


    export default {
        name: 'App',
        components: {
            NavBar,
            FileUpload,
            CalculatedDistance
        },
        data: function () {
            return {
                runningData: null,
                distanceJsonFile: 'distance.json',

                years: [{ id: null, text: 'All Years' }],
                selectedYear: null,
                selectedMonth: null,

                step1_uploadFile: true,
                step2_viewResults: false
            }
        },
        methods: {
            updateRunningData(e) {
                this.runningData = e;
            }

        },
        mounted: function () {
            // Listen for the event and its payload.
            EventBus.$on('month-selected-changed', val => {
                this.selectedMonth = val.id;
            });
             EventBus.$on('year-selected-changed', val => {
                 this.selectedYear = val.id;
            });

        }
    }
</script>

<style>

    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
