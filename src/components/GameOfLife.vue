<template>
    <canvas id="game" :width="canvasWidth" :height="canvasHeight"></canvas>
</template>
<script>
export default {
    name: 'GameOfLife',
    data() {
        return {
            cells: [],
            canvas: null,
            canvasWidth: 512,
            canvasHeight: 512
        }
    },
    mounted() {
        this.canvas = document.getElementById('game').getContext("2d");
        this.canvas.strokeStyle = "#e1e1e1";
        this.canvas.fillStyle = "cadetblue";
        this.fillCanvasSet_GliderGun();
    },
    methods: {
        fillCanvasRandom(){

        },
        fillCanvasSet_GliderGun() {
            for (let i=0; i<64; i++) {
                this.cells[i] = [];
                for (let j=0; j<64; j++) {
                    this.cells[i][j] = 0;
                }
            }
            
            // Gosper glider gun
            [
                [1, 5],[1, 6],[2, 5],[2, 6],[11, 5],[11, 6],[11, 7],[12, 4],[12, 8],[13, 3],
                [13, 9],[14, 3],[14, 9],[15, 6],[16, 4],[16, 8],[17, 5],[17, 6],[17, 7],
                [18, 6],[21, 3],[21, 4],[21, 5],[22, 3],[22, 4],[22, 5],[23, 2],[23, 6],
                [25, 1],[25, 2],[25, 6],[25, 7],[35, 3],[35, 4],[36, 3],[36, 4],
            ]
            //This marks the above cells as "Alive"
            .forEach((point) => {
                this.cells[point[0]][point[1]] = 1;
            });
            this.update();
        },
        update() { 
            let result = [];
            
            //Return amount of alive neighbours for a cell
            let _countNeighbours = (x, y) => {
                let amountAlive = 0;
                let _isFilled = (x, y) => {
                    return this.cells[x] && this.cells[x][y];
                }
                
                if (_isFilled(x-1, y-1)) amountAlive++;
                if (_isFilled(x,   y-1)) amountAlive++;
                if (_isFilled(x+1, y-1)) amountAlive++;
                if (_isFilled(x-1, y  )) amountAlive++;
                if (_isFilled(x+1, y  )) amountAlive++;
                if (_isFilled(x-1, y+1)) amountAlive++;
                if (_isFilled(x,   y+1)) amountAlive++;
                if (_isFilled(x+1, y+1)) amountAlive++;
                
                return amountAlive;
            }
            
            this.cells.forEach((row, x) => {
                result[x] = [];
                row.forEach((cell, y) => {
                    let alive = 0,
                        count = _countNeighbours(x, y);
                    
                    if (cell > 0) {
                        alive = count === 2 || count === 3 ? 1 : 0;
                    } else {
                        alive = count === 3 ? 1 : 0;
                    }
                    
                    result[x][y] = alive;
                });
            });
            
            this.cells = result;

            this.draw();
        },
        draw() {
            this.canvas.clearRect(0, 0, this.canvasWidth, this.canvasHeight);
            this.cells.forEach((row, x) => {
                row.forEach((cell, y) => {
                    this.canvas.beginPath();
                    this.canvas.rect(x*8, y*8, 8, 8);
                    if (cell) {
                        this.canvas.fill();
                    } else {
                        this.canvas.stroke();
                    }
                });
            });
            setTimeout(() => {
                this.update();
            }, 15);
        }
    }
}

</script>
<style scoped>
canvas {    
    width: 100%
}
</style>

