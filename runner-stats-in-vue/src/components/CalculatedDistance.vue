<template>
    <div class="calculated">
        <div class="calculated-group">
            <div>
                <h1>Total<br/>Distance Run</h1>
                <h2>{{milesTraveled | toFixedPoint}} miles</h2>
                <h1>From {{minDate | formatDate}} to {{maxDate | formatDate}} </h1>
            </div>
        </div>

        <div class="calculated-group">
            <div>
                <h1>Total<br/>Calories Burned</h1>
                <h2>{{caloriesTotal}} calories</h2>
            </div>
        </div>

        <div class="calculated-group">
            <div>
                <h1>Best<br/>Pace</h1>
                <h2>{{minPace}}</h2>
            </div>
        </div>

        <div class="time-to-travel">
            <h1>Time to Travel</h1>
            <h2>{{timeToTravel  | toFixedPoint}} hours</h2>
        </div>
    </div>
</template>

<script>
    import { EventBus } from './../eventbus.js';
    //import axios from 'axios';
    import moment from 'moment';

    export default {
        name: 'CalculatedDistance',
        props: {
            fileData: Array,
            yearFilter: Number,
            monthFilter: Number
        },
        data: function () {
            return {

                // jsonDistance: null,
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
                this.findYears();
                this.filterData();

            },
            yearFilter: function () {
                this.filterData();
            },
            monthFilter: function () {
                this.filterData();
            }
        },
        methods: {
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

                    if (_date.isValid()) {
                        this.dates.push(_date)
                    }

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

                EventBus.$emit('get-distance-changed', this.milesTraveled);

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
                if (this.fileData != null) {
                    for (var i = 0; i < this.fileData.length; i++) {

                        var _date = moment(this.fileData[i].date);
                        if (_date.isValid()) {
                            _dates.push(_date)
                        }
                    }

                    var _minYear = moment.min(_dates).year();
                    var _maxYear = moment.max(_dates).year();

                    var yearsArray = [];

                    for (var y = _maxYear; y >= _minYear; y--) {
                        yearsArray.push({ id: y, text: y.toString() });
                    }

                    EventBus.$emit('years-dropdown-update', yearsArray);
                }
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
            this.findYears();
            this.filterData();
        }
    }


</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    h1 {
        font-size: 1.45em;
    }

    h2 {
        font-size: 3em;
    }

    .calculated {
        margin-top: 100px;
        position: relative;
        width: 90%;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
        justify-content: center;
        align-items: flex-start;
        text-align: left;
    }

    .calculated-group {
        flex-grow: 1;
        display: flex;
        display: inline-block;
        margin: 0;
        text-align: center;
    }

        .calculated-group div {
            text-align: left;
            display: inline-block;
        }
    .time-to-travel {
        position: absolute;
        bottom: -100px;
        right: 0;
    }

    .time-to-travel h1,
    .time-to-travel h2 {
        display: inline-block;
    }

        .time-to-travel h1 {
        padding-right: 15px;
        }
</style>
