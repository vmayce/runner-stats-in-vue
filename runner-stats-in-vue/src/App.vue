<template>
    <div id="app">
        <NavBar />
        <FileUpload @updateFileUpload="updateRunningData" />
        <CalculatedDistance :fileData="runningData" jsonFileName="distance.json" />

    </div>
</template>

<script>
    import NavBar from './components/navigation/NavBar.vue'
    import FileUpload from './components/inputs/FileUpload.vue'
    import CalculatedDistance from './components/CalculatedDistance.vue'

    export default {
        name: 'App',
        components: {
            NavBar,
            FileUpload,
            CalculatedDistance
        },
        data: function () {
            return {
                runningData: null
            }
        },
        methods: {
            updateRunningData(e) {
                console.log("update running data" + e)
                this.runningData = e;
            }
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
