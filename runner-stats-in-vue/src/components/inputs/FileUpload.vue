


<template>
    <div class="file-upload-section">
        <transition name="fade">
            <div v-if="show">
                <input id="file" type="file" @change="readSingleFile" />
                <label for="file">Upload Tab Delimited File</label>
                <V-Button @click.native="getTestData">View With Test Data</V-Button>
            </div>
        </transition>
    </div>
</template>

<script>

    import Button from '../../components/ui/Button.vue'
    import axios from 'axios';

    export default {
        name: 'FileUpload',
        components: {
            'V-Button': Button
        },
        props: {
            //none at this time
        },
        data: function () {
            return {
                expectedHeaders: ["date", "distance", "time", "avg pace", "calories"],
                headersDict: { "date": null, "distance": null, "time": null, "avg pace": null, "calories": null },
                runningData: [],

                show: false
            }
        },
        methods: {
            readSingleFile: function (e) {
                var file = e.target.files[0];
                if (!file) {
                    return;
                }

                var jso = this.pushToJson;
                var reader = new FileReader();
                reader.onload = function (e) {
                    var contents = e.target.result;
                    jso(contents);
                };
                reader.readAsText(file);
            },

            pushToJson: function (contents) {
                if (contents.length == 0) {
                    //error
                }

                var contentData = contents.split(/\r?\n/);

                //verify expected headers
                var headers = this.expectedHeaders;
                this.setHeadersDictToDefaults();
                var thisHeadersDict = this.headersDict;

                if (contentData.length > 0) {
                    var testForHeaders = contentData[0].split(/\t/);

                    for (var i = 0; i < testForHeaders.length; i++) {
                        var item = testForHeaders[i].toLowerCase().trim();

                        if (headers.includes(item)) {
                            thisHeadersDict[item] = i
                        }
                    }
                }
                else {
                    //no new lines found...
                }

                this.runningData = [];
                var runningData = this.runningData;

                //as long as there are other lines
                if (contentData.length > 1) {
                    for (var j = 1; j < contentData.length; j++) {
                        var temp = contentData[j].split(/\t/);

                        runningData.push({
                            "date": temp[thisHeadersDict.date],
                            "distance": temp[thisHeadersDict.distance],
                            "time": temp[thisHeadersDict.time],
                            "avg pace": temp[thisHeadersDict["avg pace"]],
                            "calories": temp[thisHeadersDict.calories]
                        });
                    }
                }

                this.$emit('updateFileUpload', this.runningData)
            },
            setHeadersDictToDefaults: function () {
                this.headersDict = { "date": null, "distance": null, "time": null, "avg pace": null, "calories": null };
            },
            getTestData: function () {

                var jso = this.pushToJson;
                axios.get('data/testdata.txt', { baseURL: window.location.origin })
                    .then((response) => {
                        jso(response.data);

                    })
                    .catch((error) => {
                        throw error.response.data;
                    });

            }
        },
        mounted: function () {
            this.show = !this.show
        }
    }

</script>


<style scoped>

    body {
        margin: 0 !important;
    }

    .file-upload-section {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
    }

    /*https://tympanus.net/codrops/2015/09/15/styling-customizing-file-inputs-smart-way/*/
    input {
        width: 0.1px;
        height: 0.1px;
        opacity: 0;
        overflow: hidden;
        position: absolute;
        z-index: -1;
    }

        input + label {
            font-size: 2em;
            font-weight: 700;
            color: white;
            background-color: #DE9E36;
            display: block;
            cursor: pointer;
            padding: 30px;
            margin: 3px;
            margin-bottom: 50px;
            border-radius: 30px;
        }

            input:focus + label,
            input + label:hover {
                background-color: #DEB841;
                outline: 1px dotted #000;
                outline: -webkit-focus-ring-color auto 5px;
            }

            input + label * {
                pointer-events: none;
            }

    .fade-enter-active, .fade-leave-active {
        transition: opacity 1s;
    }

    .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
        opacity: 0;
    }
</style>
