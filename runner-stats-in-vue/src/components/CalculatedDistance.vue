<template>
    <div class="hello">
        <p v-for="(item, idx) in jsonDistance" :key="idx">
            {{item.crow_flies}}
        </p>
        <h1>Original Data</h1>
        {{fileData}}
        <h1>Filtered Data</h1>
        {{filteredData}}
        <h1>hello</h1>
    </div>
</template>

<script>
    import axios from 'axios';
    import moment from 'moment';

    export default {
        name: 'CalculatedDistance',
        props: {
            jsonFileName: String,
            fileData: Array,
            yearFilter: Number,
            monthFilter: Number
        },
        data: function () {
            return {
                file: this.jsonFileName,
                jsonDistance: null,
                milesTraveled: 0,
                timeToTravel: 0
            }
        },
        computed: {
            filteredData: function () {
                if (this.fileData === null && this.fileData < 1) {
                    return [];
                }
                var _yearFilter = this.yearFilter;
                var _monthFilter = this.monthFilter;
                return this.fileData.filter(function (run) {
                    var _testDate = moment(run.date);

                    return (_testDate.year() === _yearFilter || _yearFilter === null)
                        && (_testDate.month() === _monthFilter || _monthFilter === null);
                });
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
            getTotalDistanceTraveled: function () {

            }

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
