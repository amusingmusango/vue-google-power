<template>
  <!-- Configure "view" prop for QLayout -->
  <q-layout>
    <div class="layout-padding">
      <h1>The Power of Google Tutorial</h1>
      <hr/>
      <h3 style="font-weight: 100">(Huge disclaimer, my code is extremely average)</h3>
      <h4>Google Place API Demonstration</h4>
      <h5>This section will demonstrate making a called to Google Places API and displaying data through data binding.
        In order to get this working, you will need the following:
      </h5>
      <ul>
        <li>
          <h5>Chrome Extension <a href="https://chrome.google.com/webstore/detail/cors-toggle/jioikioepegflmdnbocfhgmpmopmjkim?hl=en" target="_blank"> CORS Toggle</a>
          (This should be turned on which making calls to the Google API due to CORS header issues)
          </h5>
        </li>
        <li><h5>Google <a href="https://developers.google.com/maps/documentation/javascript/get-api-key" target="_blank">API Key</a> (The API Key should be inserted into the code in Hello.vue)</h5></li>
      </ul>
      <div class="row">
        <div class="col-lg-6">
          <q-search inverted v-model="search" @keyup.enter="searchForPlace()" color="amber"/>
        </div>
      </div>

      <q-list>
        <q-collapsible icon="search" label="Results">
          <div>
            <div class="row">
              <div class="col-lg-4" v-for="(place, index) in places">
                <p>{{place.name}}</p>
                <img :src="placePhotos[index]" class="place-photos">
              </div>
            </div>
          </div>
        </q-collapsible>
      </q-list>
      <h5>To get google place details read through the google place api <a href="https://developers.google.com/places/web-service/details" target="_blank"> documentation</a></h5>
      <h4>Firebase Creating and deleting demo</h4>
      <h5>The following gives you a brief demonstration on how to integrate firebase into your view application. I highly recommend going through this <a href="https://medium.com/codingthesmartway-com-blog/vue-js-2-firebase-e4b2479e35a8" target="_blank"> tutorial</a>
        to get a solid understanding of how the code works</h5>
      <div class="row">
        <div class="col-lg-6">
          <q-input  inverted v-model="newBook.title" color="amber" placeholder="Title"/>
        </div>
        <div class="col-lg-6">
          <q-input  inverted v-model="newBook.author" color="amber" placeholder="Author"/>
          <q-btn color="primary" @click="addBook()" class="close-modal-btn full-width" big>
            Add Book
          </q-btn>
        </div>

        <div class="col-lg-12">
          <br/><br/>
          <table class="demo-table">
            <thead>
            <tr>
              <th>Title</th>
              <th>Author</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="book in books">
              <td>
                {{book.author}}
              </td>
              <td>
                {{book.title}}
              </td>

              <td>
                <q-btn color="primary"  v-on:click="removeBook(book)">
                  Delete
                </q-btn>
              </td>
            </tr>
            </tbody>

          </table>
        </div>
      </div>



    </div>

  </q-layout>
</template>

<script>
  import Firebase from 'firebase'
  import { FIREBASE_CONFIG } from '../firebase/FirebaseConfig'
  // Initialization of app
  let app = Firebase.initializeApp(FIREBASE_CONFIG)
  // Connecting to Database
  let db = app.database()
  // Connecting to database object books
  let booksRef = db.ref('books')
  export default {
    firebase: {
      books: booksRef
    },
    data () {
      return {
        apiKey: 'AIzaSyC8RYqR23faRWnMujTxfmE8bc92ZnX3SiI',
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
        this.$http.get('https://maps.googleapis.com/maps/api/place/textsearch/json?query=' + search + '&key=' + this.apiKey).then((response) => {
          console.log(response.data.results)
          let places = response.data.results
          for (let placeIndex = 0; placeIndex < places.length; ++placeIndex) {
            this.$set(this.places, placeIndex, places[placeIndex])
            console.log(this.places[placeIndex])
            this.currentPhotoReference = places[placeIndex].photos[0].photo_reference
            this.$set(this.placePhotos, placeIndex, 'https://maps.googleapis.com/maps/api/place/photo?photoreference=' + this.currentPhotoReference + '&key=' + this.apiKey + '&maxheight=200')
          }
        })
      },
      addBook: function () {
        booksRef.push(this.newBook)
        this.newBook.title = ''
        this.newBook.author = ''
      },
      removeBook: function (book) {
        booksRef.child(book['.key']).remove()
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
