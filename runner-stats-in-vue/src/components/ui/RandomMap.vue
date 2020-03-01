<template>
    <div>
        <div id="mapid"></div>
    </div>
</template>


<script>
    import { EventBus } from './../../eventbus.js';
    import RandomLocation from 'random-location'
    import L from 'leaflet'
    //import axios from 'axios'
    //import convert from 'xml-js'

    export default {
        name: 'RandomMapLocation',

        data: function () {
            return {
                distance: 0,

                randomDestination: null,
                randomDestinationName: '',
                randomDestinationObject: null,

                randomStart: null,
                randomStartName: '',
                randomStartObject: null,

                mapView: null
            }
        },
        methods: {
            createMap: function () {

                if (this.mapView != null) {
                    this.mapView.remove();
                }

                this.mapView = L.map('mapid').setView(this.randomDestination, 13);

                L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
                }).addTo(this.mapView);

                L.marker(this.randomDestination).addTo(this.mapView)
                    .bindPopup('You could have ended up all the way over here!');


                L.marker(this.randomStart).addTo(this.mapView)
                    .bindPopup('If you start here...')
                    .openPopup();

                
                // create a red polyline from an array of LatLng points
                var latlngs = [
                    this.randomStart,
                    this.randomDestination
                ];
                var polyline = L.polyline(latlngs, { color: 'red' }).addTo(this.mapView);
                // zoom the map to the polyline
                this.mapView.fitBounds(polyline.getBounds().pad(0.5));
            },
            getRandomLocation: function () {
                // Twitter HQ
                const P = {
                    latitude: 37.7768006,
                    longitude: -122.4187928
                }

                const randomDist = Math.random(2000);

                const R = this.distance * 1609.34 // meters

                this.randomStartObject = RandomLocation.randomCircumferencePoint(P, randomDist)
                this.randomStart = [this.randomStartObject.latitude, this.randomStartObject.longitude]

                this.randomDestinationObject = RandomLocation.randomCircumferencePoint(this.randomStartObject, R)
                this.randomDestination = [this.randomDestinationObject.latitude, this.randomDestinationObject.longitude]

            },
            //getCity: async function (latitude, longitude) {

                //TODO: Move this to Node.js backend server.
                //Per the policy of usage, the referer / user-agent must be set which cannot be done in
                //  the javascript frontend.

                //await this.sleep(5000)

                ////https://www.beyondjava.net/how-to-use-reverse-geolocation-of-openstreetmap
                //var options = {
                //    url: 'https://nominatim.openstreetmap.org/reverse?format=xml&lat=' + latitude + '&lon=' + longitude + '&zoom=18&addressdetails=1',
                //    method: 'get'
                //}

                //return axios(options)
                //    .then(function (response) {

                //        var body = response.data;

                //        const result1 = convert.xml2json(body, { compact: true, spaces: 4 });
                //        const geo = JSON.parse(result1);

                //        if (geo.reversegeocode.addressparts.town) {
                //            return (geo.reversegeocode.addressparts.town._text);
                //        } else if (geo.reversegeocode.addressparts.city) {
                //            return (geo.reversegeocode.addressparts.city._text);
                //        } else if (geo.reversegeocode.addressparts.village) {
                //            return (geo.reversegeocode.addressparts.village._text);
                //        } else if (geo.reversegeocode.addressparts.hamlet) {
                //            return (geo.reversegeocode.addressparts.hamlet._text);
                //        } else {
                //            console.log("An error occurred - couldn't find the address of the GPS coordinates");
                //            console.log(geo.reversegeocode.addressparts);
                //            return "";
                //        }

                //    });
                //https://operations.osmfoundation.org/policies/nominatim/

            //},
            sleep: function (ms) {
                //https://stackoverflow.com/questions/54955426/how-to-use-async-await-in-vue-js
                return new Promise(resolve => setTimeout(resolve, ms));
            }
        },
        mounted: function () {
            EventBus.$on('get-distance-changed', val => {
                this.distance = val;

                this.getRandomLocation()
                this.createMap();
            });


            //this.randomDestinationName = this.getCity(37, -122);
        }
    }
</script>


<style scoped>
    #mapid {
        height: 300px;
        width: 50%;
    }
</style>


