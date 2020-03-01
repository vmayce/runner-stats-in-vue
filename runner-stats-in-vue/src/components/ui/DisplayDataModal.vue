<template>

    <div id="myModal" class="modal" v-if="display" v-click-outside="onClickOutside">
        <span class="close" @click="closeButton">&times;</span>
        <!-- Modal content -->
        <div class="modal-content">

            <p v-for="(item, idx) in dataArray" :key="idx">
                {{item}}
            </p>
        </div>

    </div>


</template>

<script>
    import { EventBus } from './../../eventbus.js';
    import vClickOutside from 'v-click-outside'

    export default {
        name: 'DisplayDataModal',
        props: {
            dataArray: Array
        },
        data: function () {
            return {
                display: false
            }
        },
        directives: {
            clickOutside: vClickOutside.directive
        },
        methods: {
            closeButton: function () {
                this.display = !this.display;
            },
            onClickOutside(event) {
                console.log('Clicked outside. Event: ', event)
            }
        },
        mounted: function () {
            // Listen to display
            EventBus.$on('show-uploaded-data', val => {
                this.display = val;
            });
        }
    }


</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    /* The Modal (background) */
    .modal {
        display: block; /* Hidden by default */
        position: fixed; /* Stay in place */
        z-index: 1500; /* Sit on top */
        padding-top: 100px; /* Location of the box */
        left: 0;
        top: 0;
        width: 100%; /* Full width */
        height: 100%; /* Full height */
        background-color: #fefefe; /* Fallback color */
        background-color: rgba(255, 255, 255, 0.5); /* Black w/ opacity */
    }

    /* Modal Content */
    .modal-content {
        position: relative;
        background-color: #fefefe;
        margin: auto;
        padding: 20px;
        width: 80%;
        height: 60%;
        overflow: auto; /* Enable scroll if needed */
        border-radius: 15px;
        box-shadow:0 10px 16px 0 rgba(0,0,0,0.2),0 6px 20px 0 rgba(0,0,0,0.19) !important;
    }

    /* The Close Button */
    .close {
        position: absolute;
        top: 5%;
        color: white;
        float: right;
        font-size: 28px;
        font-weight: bold;
        font-size: 2em;
        background-color: rgba(148, 148, 148, 0.42);
        padding: 25px;
        height: 50px;
        width: 50px;
        margin: 0;
        border-radius: 50px;
        z-index:1501
    }

        .close:hover,
        .close:focus {
            text-decoration: none;
            cursor: pointer;
            background-color: #DE9E36;
            opacity: .7;
        }
</style>
