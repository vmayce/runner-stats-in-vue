<template>
    <div class="hello">
        <p v-for="(item, idx) in jsonDistance" :key="idx">
            {{item.crow_flies}}
        </p>
        {{fileData}}
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        name: 'CalculatedDistance',
        props: {
            jsonFileName: String,
            fileData: []
        },
        data: function () {
            return {
                file: this.jsonFileName,
                jsonDistance: null
                
            }
        },
        methods: {
            getJsonFile: function () {
                axios.get('data/' + this.file, { baseURL: window.location.origin })
                    .then((response) => {
                        this.jsonDistance = response.data;
                    })
                    .catch((error) => {
                        throw error.response.data;
                    });
            },

        },
        mounted: function () {
            this.getJsonFile();
        }
    }
    

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

    h3 {
        margin: 40px 0 0;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #42b983;
    }
</style>
