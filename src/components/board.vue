<template>
    <div  v-if="isMobile" class = "BBMobile">
        <div class = "board">
            <canvas @mouseup="mouseUP" @mousemove="movedOnBoard" @mousedown="mouseDOWN" ref="mainBoard" width="self.width-30" height="self.width-30"></canvas>
        </div>
        <div class = "button" @click="submit">
            Submit
        </div>
    </div>   
    <div v-else class = "BB">
        <div class = "board">
            <canvas @mouseup="mouseUP" @mousemove="movedOnBoard" @mousedown="mouseDOWN" ref="mainBoard" width="600" height="600"></canvas>
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
    props: ["initTiles", "width", "height"],
    methods: {
        submit() {
            this.$emit('board-send', this.tiles);
        },
        movedOnBoard(click){
            if (!this.mousePressed) return;
            var x = click.offsetX;
            var y = click.offsetY;
            var i = Math.floor(y/(this.height/this.gridNum));
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
            var i = Math.floor(y/(this.height/this.gridNum));
            var j = Math.floor(x/(this.width/this.gridNum));
            if (!this.tiles[i][j][1]){
                this.tiles[i][j][0] = !this.tiles[i][j][0];
                this.tiles[i][j][1] = true;
            }
            this.drawTiles();
        },
        drawTiles(){
            var board = this.$refs["mainBoard"];
            var c = board.getContext("2d");
            var size = this.height/this.gridNum;
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
                    c.fillRect(x, y, size, size);
                    c.stroke();
                }
            }
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
        var board = this.$refs["mainBoard"];
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
    }
}
</script>

<style>
.board {
  padding: 15px;
  text-align: center;
  background: #226356;
  color: white;
  font-size: 30px;
}
.button {
  padding: 15px;
  text-align: center;
  background: #000000;
  color: white;
  font-size: 30px;
}
.BBMobile{
    height: 80%;
}
.BB{
    height: 100%;
}

</style>