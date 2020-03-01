<template>
    <div class="hello">
        <p v-for="(item, idx) in jsonDistance" :key="idx">
            {{item.crow_flies}}
        </p>




        <h1>Original Data</h1>
        {{fileData}}
        <h1>Filtered Data</h1>
        {{filteredFileData}}
        <h1>hello</h1>

        <h1>From {{minDate | formatDate}} to {{maxDate | formatDate}} </h1>
        <h2>Total Distance Run</h2>
        <h1>{{milesTraveled | toFixedPoint}} miles</h1>

        <h2>Total Calories Burned</h2>
        <h1>{{caloriesTotal}} calories</h1>

        <h2>Best Pace</h2>
        <h1>{{minPace}}</h1>

        <h2>Time to Travel</h2>
        <h1>{{timeToTravel  | toFixedPoint}} hours</h1>
    </div>
</template>

<script>
    import { EventBus } from './../eventbus.js';
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
                filteredFileData: [],
                milesTraveled: 0.0,
                timeToTravel: 0.0,
                caloriesTotal: 0,
                minPace: '99:99',
                dates: [],
                minDate: '',
                maxDate: ''
            }
        },
        watch: {
            fileData: function () {
                this.filterData();
                this.findYears();
            },
            yearFilter: function () {
                this.filterData();
            },
            monthFilter: function () {
                this.filterData();
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
            filterData: function () {
                if (this.fileData === null && this.fileData < 1) {
                    return [];
                }
                var _yearFilter = this.yearFilter;
                var _monthFilter = this.monthFilter;
                this.filteredFileData = this.fileData.filter(function (run) {
                    var _testDate = moment(run.date);

                    return (_testDate.year() === _yearFilter || _yearFilter === null)
                        && (_testDate.month() === _monthFilter || _monthFilter === null);
                });

                this.getResultStats();
            },

            getResultStats: function () {

                this.setDefaults();

                var timeRegex = '[0-9]{1,}:[0-5][0-9]:[0-5][0-9]$';
                var paceRegex = '[0-9]?[0-9]:[0-9]{2}$';

                var _milesTotal = 0;
                var _timeTotal = 0;
                var _calorieTotal = 0;
                var _minPace = '99:99';
                var _filteredFileData = this.filteredFileData;
                for (var i = 0; i < _filteredFileData.length; i++) {

                    var _date = moment(_filteredFileData[i].date);
                    this.dates.push(_date)


                    var _distance = Number.parseFloat(_filteredFileData[i].distance)
                    _milesTotal += Number.isNaN(_distance) ? 0 : _distance;

                    if ((_filteredFileData[i].time) != null) {

                        if ((_filteredFileData[i].time).match(timeRegex)) {
                            var time = (_filteredFileData[i].time).split(':');
                            _timeTotal += (Number.parseInt(time[0]) * 3600) + (Number.parseInt(time[1]) * 60) + Number.parseInt(time[2]) // convert to seconds and add together
                        }

                    }
                    var _calories = Number.parseInt(_filteredFileData[i].calories)
                    _calorieTotal += Number.isNaN(_calories) ? 0 : _calories;

                    var _avgPace = (_filteredFileData[i]['avg pace']);

                    _minPace = this.checkMinTime(_minPace, _avgPace, paceRegex);
                }

                this.milesTraveled = _milesTotal;
                this.timeToTravel = _timeTotal / 3600;
                this.caloriesTotal = _calorieTotal;
                this.minPace = _minPace


                this.minDate = moment.min(this.dates);
                this.maxDate = moment.max(this.dates);
            },
            setDefaults: function () {
                this.dates = [];
                this.milesTraveled = 0;
                this.timeToTravel = 0;
                this.caloriesTotal = 0;
                this.minPace = '99:99';
            },
            checkMinTime: function (min, test, regex) {

                if (test != null) {

                    if (test.match(regex)) {
                        var testSplit = test.split(':');
                        var minSplit = min.split(':');

                        if (minSplit[0] < testSplit[0]) {
                            return min;
                        }

                        if (minSplit[0] === testSplit[0] && minSplit[1] < testSplit[1]) {
                            return min;
                        }

                        return test;
                    }
                }
                return min;
            },
            findYears: function () {
                var _dates = [];
                for (var i = 0; i < this.fileData.length; i++) {

                    var _date = moment(this.fileData[i].date);
                    _dates.push(_date)
                }

                var _minYear = moment.min(this.dates).year();
                var _maxYear = moment.max(this.dates).year();

                var yearsArray = [];

                for (var y = _minYear; y <= _maxYear; y++) {
                    yearsArray.push({ id: y, text: y.toString() });
                }

                EventBus.$emit('years-dropdown-update', yearsArray);
            }

        },
        filters: {
            toFixedPoint: function (val) {
                if (Number.isNaN(Number.parseFloat(val))) { return ''; }

                return val.toFixed(2)
            },
            formatDate: function (val) {
                return moment(val).format('LL')
            }
        },
        mounted: function () {
            this.getJsonFile();
            this.findYears();
            this.filterData();
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
