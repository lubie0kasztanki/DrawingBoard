<template>
    <div class = "BB">
        <div class = "board">
            <canvas class = "canvas" @mouseup="mouseUP" @mousemove="movedOnBoard" @mousedown="mouseDOWN" ref="mainBoard" v-bind:width="this.width" v-bind:height="this.width"></canvas>
        </div>
        <div class = "button" @click="submit">
            Submit
        </div>
    </div>   
</template>

<script>
export default {
    name: "Board",
    data() {
        return {
            gridNum: 20,
            tiles: [],
            mousePressed: false,
            currentElementType: 1
        }
    },
    props: ["initTiles", "width"],
    methods: {
        submit() {
            this.$emit('board-send', this.tiles);
        },
        movedOnBoard(click){
            if (!this.mousePressed) return;
            var x = click.offsetX;
            var y = click.offsetY;
            var i = Math.floor(y/(this.width/this.gridNum));
            var j = Math.floor(x/(this.width/this.gridNum));
            if (!this.tiles[i][j][1]){
                if (this.tiles[i][j][0] == 0)
                this.tiles[i][j][0] = this.currentElementType;
                else this.tiles[i][j][0] = 0;
                this.tiles[i][j][1] = true;
            }
            this.drawTiles();
        },
        mouseDOWN(click){
            this.mousePressed = true;
            var x = click.offsetX;
            var y = click.offsetY;
            var i = Math.floor(y/(this.width/this.gridNum));
            var j = Math.floor(x/(this.width/this.gridNum));
            if (!this.tiles[i][j][1]){
                this.tiles[i][j][0] = !this.tiles[i][j][0];
                this.tiles[i][j][1] = true;
            }
            this.drawTiles();
        },
        mouseUP() {
            this.mousePressed = false;
            for (var i = 0; i < this.gridNum; i++){
                for (var j = 0; j < this.gridNum; j++){
                    this.tiles[i][j][1] = false;
                }
            }
        },
        initWithTiles() {
            for (var index = 0; index < 400; index ++){
                var i = Math.floor(index / 20);
                var j = Math.floor(index % 20);
                this.tiles[i][j][0] = this.initTiles[index];
            }
            this.drawTiles();
            this.drawGrid();
        },
        drawTiles(){
            var board = this.$refs["mainBoard"];
            var c = board.getContext("2d");
            var size = (this.width/this.gridNum); // tile size
            for (var i = 0; i < this.gridNum; i++){
                for (var j = 0; j < this.gridNum; j++){
                    var x = j*size;
                    var y = i*size;
                    if (this.tiles[i][j][0] == 0){
                        c.fillStyle = "#FFFFFF";
                    }
                    else {
                        c.fillStyle = "#000000";
                    }
                    c.fillRect(x, y, Math.ceil(size), Math.ceil(size));
                    c.stroke();
                }
            }
        },
        drawGrid(){
            var board = this.$refs["mainBoard"];
            var c = board.getContext("2d");
            c.fillStyle = "#000000";
            for (var x = 0; x <= this.width; x += (this.width/this.gridNum)){
                c.moveTo(x, 0);
                c.lineTo(x, this.width);
            }
            for (var y= 0; y <= this.width; y += (this.width/this.gridNum)){
                c.moveTo(0, y);
                c.lineTo(this.width, y);
            }
            c.stroke();
        },
    },
    mounted() {
        for (var i = 0; i < this.gridNum; i++) {
            var row = []
            for (var j = 0; j < this.gridNum; j++){
                row.push([0, false]);
            }
            this.tiles.push(row);
        }
        this.initWithTiles();
    }
}
</script>

<style>
.board {
  padding-top: 15px;
  padding-bottom: 15px;
  text-align: center;
  background: #226356;
  color: white;
  font-size: 30px;
  height: auto;
  align-self: center;
}
.button {
  padding: 15px;
  text-align: center;
  background: #000000;
  color: white;
  height: auto;
  font-size: 30px;
}
@media screen and (max-width: 480px) {
    .BB{
        height: 100%;
    }
    .button{
        position: absolute;
        text-align: center;
        bottom: 0%;
        padding-left: 0;
        width: 100%;
    }

}
</style>