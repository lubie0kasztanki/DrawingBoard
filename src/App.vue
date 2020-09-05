<template>

  <div id="app">
    <Banner />
    <Board v-bind:initTiles="workingTiles" v-on:board-send="submitBoard" v-if="inBoardSection" />
    <Map v-bind:ready="mapReady" v-on:map-selected="selected" ref = "map" v-bind:rawTilesTable="rawTilesTable" v-if="!inBoardSection" />
  </div>

</template>

<script>
//var src="https://www.gstatic.com/firebasejs/7.14.1/firebase-app.js"
import Banner from './components/banner.vue';
import Board from './components/board.vue';
import Map from './components/map';
import * as firebase from "firebase/app";
import "firebase/firestore";
import cridentials from "../firebase_cridentials/firebase_cridentials.json"


var firebaseConfig = cridentials

firebase.initializeApp(firebaseConfig);

export default {
  name: 'App',
  components: {
    Banner,
    Board,
    Map
  },
  data () {
    return {
      inBoardSection: false,
      rawTilesTable: [],
      tileLocks: [],
      currentChoice: [],
      dbObserver: null,
      mapReady: false,
      workingTiles: []
    }
  },
  methods: {
    submitBoard(tile) {      
        if (!this.validateSelection()) return;
        var serialisedTile = [];
        for (var i = 0; i < 20; i++){
          for (var j = 0; j < 20; j++){
            serialisedTile.push(tile[i][j][0]);
          }
        }
        var db = firebase.firestore();
        let tileRef = db.collection('mapTiles').doc(this.currentChoice[0] + "_" + this.currentChoice[1]);
        tileRef.set({
          pixels: serialisedTile,
          locked: false
          })
        this.inBoardSection = false;
    },
    selected(pos) {
      var i = Math.floor(pos.offsetY/60);
      var j = Math.floor(pos.offsetX/60);
      this.currentChoice = [];
      this.currentChoice.push(i);
      this.currentChoice.push(j);
      console.log(this.currentChoice);
      if (this.validateSelection()) {
        while (this.workingTiles.length > 0) this.workingTiles.pop();
        for (var n = 0; n < 400; n++){
          this.workingTiles.push(this.rawTilesTable[i * 10 + j][n]);
        }
        this.inBoardSection = true;
      }
    },
    validateSelection(){
      if (!this.mapReady) return false;
      var translatedIndex = this.currentChoice[0]*10 + this.currentChoice[1];
      if (!this.tileLocks[translatedIndex]) return true;
      else return false;
    }
  },
  mounted(){
    // var t = [];
    // for (var z = 0; z < 20; z ++){
    //   var helper = [];
    //   for (var y = 0; y < 20; y++){
    //     helper.push([0,false]);
    //   }
    //   t.push(helper);
    // }
    // for (var i1 = 0; i1< 10; i1 ++){
    //   for(var i2 = 0; i2 < 10; i2++){
    //     this.currentChoice = [];
    //     this.currentChoice.push(i1);
    //     this.currentChoice.push(i2);
    //     this.submitBoard(t);
    //   }
    // }

    var db = firebase.firestore();
    let tilesRef = db.collection('mapTiles');


    this.dbObserver = tilesRef.onSnapshot(snapshot => {
    while(this.rawTilesTable.length > 0) this.rawTilesTable.pop();
    while(this.tileLocks.length > 0) this.tileLocks.pop();
    snapshot.forEach(doc => {
      this.rawTilesTable.push(doc.data().pixels);
      this.tileLocks.push(doc.data().locked);
    });

    this.$refs["map"].extractFromRawTiles();
    this.$refs["map"].drawPixels();
    this.$refs["map"].drawGrid();
    this.mapReady = true;
    }, err => {
      console.log('Error getting documents', err);
    });  
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0px;
}
body{
  margin: 0px;
}
</style>
