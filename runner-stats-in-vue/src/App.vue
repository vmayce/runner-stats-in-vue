<template>
    <div id="app">
        <NavBar :years="years" v-if="step2_viewResults" />
        <main>
            <FileUpload @updateFileUpload="updateRunningData" v-if="step1_uploadFile" />
            <CalculatedDistance :fileData="runningData" :yearFilter="selectedYear" :monthFilter="selectedMonth" v-if="step2_viewResults" />
            <DisplayDataModal :dataArray="runningData" v-if="step2_viewResults" />

            <div v-if="step2_viewResults" class="green-running-img">

            </div>
            <!--<img src="assets/img/filip-mroz-sgtxFOiBZmQ-unsplash.png" />-->
            <RandomMapLocation v-if="step2_viewResults" />

        </main>
    </div>
</template>

<script>
    import { EventBus } from './eventbus.js';
    import NavBar from './components/navigation/NavBar.vue'
    import FileUpload from './components/inputs/FileUpload.vue'
    import CalculatedDistance from './components/CalculatedDistance.vue'
    import DisplayDataModal from './components/ui/DisplayDataModal.vue'
    import RandomMapLocation from './components/ui/RandomMap.vue'

    export default {
        name: 'App',
        components: {
            NavBar,
            FileUpload,
            CalculatedDistance,
            DisplayDataModal,
            RandomMapLocation
        },
        data: function () {
            return {
                runningData: null,

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

                this.updateStep();
            },
            updateStep() {
                this.step1_uploadFile = !this.step1_uploadFile;
                this.step2_viewResults = !this.step2_viewResults;
            },
            setToDefaults() {
                this.selectedMonth = null;
                this.selectedYear = null;
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

            EventBus.$on('years-dropdown-update', val => {
                this.years = val;
                this.years.unshift({ id: null, text: 'All Years' });
            });

            EventBus.$on('reupload-data-clicked', () => {
                this.updateStep();
                this.setToDefaults();
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
    main {
        width: 75%;
        margin: auto;
    }

    
</style>
