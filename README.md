
<img src="runner-stats-in-vue/public/assets/img/RunnerStatsCover.png" alt="runner stats logo" width="200">

# runner-stats-in-vue

This is a simple web app written in Vue that takes a tab delimited text file and shows some basic stats in a fun way to see which US city you could run to and how long it would take based on your running stats.

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

## Libraries Used

### Axios
### Moment.js
### Leaflet
### Random-Location
### Open Street Map

## Improvements to Consider in the Future

### Add in the Reverse Geocaching
One of the options to rever geocache is through Open Street Map, however, they require a custom referer and user agent when requesting the info. This only implements Vue.js in the frontend and would require impelementing a server backend to complete the requests.

### Organizing Emitted Events
I was able to use the EventBus to get the emitted events, which works well. However, the events could have been a little more organized. In a larger application I would have implemented Vuex to have better state management. 

With such a small app, it didn't mmake sense to use a full blown state management, but keeping the emitted events organized will help in the future if I decide to go a similar route and realize that Vuex would need to be implemented.

### Add in More Activity Options
This could include walking, biking, etc. :)
