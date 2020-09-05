<template>
    <div v-if="isMobile" class = "mainMap">
        <canvas @click="clickedOnTile" @mousemove="hover" ref="mainMap" width="screen.width-30" height="screen.width-30"></canvas>
    </div>    
    <div v-else class = "mainMap">
        <canvas @click="clickedOnTile" @mousemove="hover" ref="mainMap" width="600" height="600"></canvas>
    </div> 
</template>
<script>
export default {
    name: "Map",
    data(){
        return{
            tilesTables: [],
            gridNum: 10,
            tileSize: 0,
        }
    },
    props: ["rawTilesTable", "ready", "height", "width"],
    methods: {
        clickedOnTile(click) {
            if (this.ready) this.$emit("map-selected", click);
        },
        hover(pos) {
            console.log(this.ready);
            if (!this.ready) return;
            this.drawPixels();
            this.drawGrid();
            var board = this.$refs["mainMap"];
            var c = board.getContext("2d");
            var x = Math.floor(pos.offsetX/this.tileSize) * this.tileSize;
            var y = Math.floor(pos.offsetY/this.tileSize) * this.tileSize;
            c.fillStyle = 'rgba(200,200,200,0.5)';
            console.log(x,y);
            if (x < this.gridNum * this.tileSize && y < this.gridNum * this.tileSize){
                c.fillRect(x,y,this.tileSize,this.tileSize);
            }
            
        },
        extractFromRawTiles() {
            this.tilesTables = [];
            var mainIndex = -1;
            for (var i = 0; i < 100; i++){
                
                if (i % 10 == 0){
                    mainIndex ++;
                    this.tilesTables.push([]);
                }
                var tile = [];
                for (var j = 0; j < 20; j++){
                    tile.push(this.rawTilesTable[i].slice(j*20, j*20 + 20));
                }
                this.tilesTables[mainIndex].push(tile);
            }
        },
        drawPixels(){
            var board = this.$refs["mainMap"];
            var c = board.getContext("2d");
            for (var i = 0; i < this.gridNum; i++){
                for (var j = 0; j < this.gridNum; j++){
                    for (var k = 0; k < 20; k++){
                        for (var l = 0; l < 20; l++){
                            var x = j * this.tileSize + l * (this.tileSize/20);
                            var y = i * this.tileSize + k * (this.tileSize/20);
                            if (this.tilesTables[i][j][k][l] == 1){
                                c.fillStyle = "#000000";
                                c.fillRect(x,y,3,3);
                            } 
                            else {
                                c.fillStyle = "#FFFFFF";
                                c.fillRect(x,y,3,3);
                            }
                        }
                    }
                }
            }
        },
        drawGrid(){
        var board = this.$refs["mainMap"];
        var c = board.getContext("2d");
        for (var x = 0; x <= this.width; x += (this.width/this.gridNum)){
            c.moveTo(x, 0);
            c.lineTo(x, this.height);
        }
        for (var y= 0; y <= this.height; y += (this.height/this.gridNum)){
            c.moveTo(0, y);
            c.lineTo(this.width, y);
        }
        c.stroke();
        },
    },
    mounted() {
        this.tileSize = this.height/this.gridNum
        console.log("mounted map", this.ready);
        if (this.ready){
            
            this.extractFromRawTiles();
            this.drawPixels();
            this.drawGrid();
        }
    }  
}
</script>
<style>
.mainMap {
  padding: 15px;
  text-align: center;
  background: #226356;
  color: white;
  font-size: 30px;
}

</style>