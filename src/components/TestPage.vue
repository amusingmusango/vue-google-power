<template>
  <!-- Configure "view" prop for QLayout -->
  <q-layout>
    <div class="layout-padding">
      <h4>Google Place API CORS Issue</h4>
      <div class="row">
        <div class="col-lg-6">
          <p>This is making a call to the google places api. In the search box, type "Pubs in cape town"</p>
          <q-search inverted v-model="search" @keyup.enter="searchForPlace()" color="amber"/>
        </div>
      </div>

      <div class="row">
        <div class="col-lg-4" v-for="(place, index) in places">
          <p>{{place.name}}</p>
          <img :src="placePhotos[index]" class="place-photos">
        </div>
      </div>
    </div>

  </q-layout>
</template>

<script>
  let config = {
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Credentials': 'true',
      'Access-Control-Allow-Methods': 'GET,POST,PUT,DELETE,OPTIONS',
      'Access-Control-Allow-Headers': 'Access-Control-Allow-Headers: Origin, Content-Type, X-Auth-Token'
    }
  }
  export default {
    data () {
      return {
        apiKey: 'AIzaSyCTTAtNZZMGWzMamSMc9khwIc5DKsYIBlg',
        search: '',
        pageToken: '5',
        places: [],
        currentPhotoReference: '',
        placePhotos: [],
        newBook: {
          title: '',
          author: ''
        }
      }
    },
    methods: {
      searchForPlace: function () {
        console.log('test')
        let search = this.search
        this.$http.get('https://maps.googleapis.com/maps/api/place/textsearch/json?query=' + search + '&key=' + this.apiKey, config).then((response) => {
          console.log(response.data.results)
          let places = response.data.results
          for (let placeIndex = 0; placeIndex < places.length; ++placeIndex) {
            this.$set(this.places, placeIndex, places[placeIndex])
            console.log(this.places[placeIndex])
            this.currentPhotoReference = places[placeIndex].photos[0].photo_reference
            this.$set(this.placePhotos, placeIndex, 'https://maps.googleapis.com/maps/api/place/photo?photoreference=' + this.currentPhotoReference + '&key=' + this.apiKey + '&maxheight=200')
          }
        })
      }
    }
  }
</script>

<style>

  .place-photos {
    height:100px;
    width: 100px;
  }

  .demo-table {
    width: 50%;
    text-align: left;
    font-size: x-large;
  }
</style>
