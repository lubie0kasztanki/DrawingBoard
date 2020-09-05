<template>
    <div class = "mainMap">
        <canvas @click="clickedOnTile" @mousemove="hover" ref="mainMap" width="600" height="600"></canvas>
    </div>    
</template>
<script>
export default {
    name: "Map",
    data(){
        return{
            tilesTables: [],
            height: 600,
            width: 600,
            gridNum: 10,
        }
    },
    props: ["rawTilesTable", "ready"],
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
            var x = Math.floor(pos.offsetX/60) * 60;
            var y = Math.floor(pos.offsetY/60) * 60;
            c.fillStyle = 'rgba(200,200,200,0.5)';
            console.log(x,y);
            c.fillRect(x,y,60,60);
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
            for (var i = 0; i < 10; i++){
                for (var j = 0; j < 10; j++){
                    for (var k = 0; k < 20; k++){
                        for (var l = 0; l < 20; l++){
                            var x = j * 60 + l * 3;
                            var y = i * 60 + k * 3;
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