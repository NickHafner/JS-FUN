<template>
    <div>
        <button @click="stop">stop</button>
        <button @click="start">start</button>
        <canvas id="game" :width="canvasWidth" :height="canvasHeight"></canvas>
    </div>
</template>
<script>
export default {
    name: 'GameOfLife',
    data() {
        return {
            cells: [],
            continue: false,
            canvas: null,
            canvasWidth: 812,
            canvasHeight: 812,
            gameWidth: 100,
            gameHeight: 100
        }
    },
    mounted() {
        this.canvas = document.getElementById('game').getContext("2d");
        this.canvas.strokeStyle = "#e1e1e1";
        this.canvas.fillStyle = "cadetblue";
        this.fillCanvasSet_R_pentomino();
    },
    methods: {
        setUpCanvas() {
            for (let i=0; i<this.gameWidth; i++) {
                this.cells[i] = [];
                for (let j=0; j<this.gameHeight; j++) {
                    this.cells[i][j] = 0;
                }
            }
        },
        fillCanvasRandom(){

        },
        fillCanvasSet_GliderGun() {
            this.setUpCanvas();
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

            this.draw();
        },
        fillCanvasSet_Acorn() {
            this.setUpCanvas();            
            // Acorn
            [
                [72, 64],[73,64],[73,62],[75,63],[76,64],[77,64],[78,64]
            ]
            //This marks the above cells as "Alive"
            .forEach((point) => {
                this.cells[point[0]][point[1]] = 1;
            });

            this.draw();
        },
        fillCanvasSet_R_pentomino() {
            this.setUpCanvas();            
            // The R-pentomino
            [
                [42, 48],[43,48],[43,49],[43,47],[44,47]
            ]
            //This marks the above cells as "Alive"
            .forEach((point) => {
                this.cells[point[0]][point[1]] = 1;
            });

            this.draw();
        },
        fillCanvasSet_Diehard() {
            this.setUpCanvas();            
            // Diehard pattern x4
            [
                [70, 24],[71,24],[71,25],[75,25],[76,25],[77,25],[76,23],
                [30, 24],[31,24],[31,25],[35,25],[36,25],[37,25],[36,23],
                [70, 64],[71,64],[71,65],[75,65],[76,65],[77,65],[76,63],
                [30, 64],[31,64],[31,65],[35,65],[36,65],[37,65],[36,63]
            ]
            //This marks the above cells as "Alive"
            .forEach((point) => {
                this.cells[point[0]][point[1]] = 1;
            });

            this.draw();
        },
        fillCanvasSet_StillLife() {
            this.setUpCanvas();
            // Still Life
            [
                [1, 5],[1, 6],[2, 5],[2, 6],
                [6, 6],[7, 5],[7, 7],[8, 5],[8, 7],[9, 6],
                [1, 12],[2,11],[2,13],[3,11],[3,14],[4,12],[4,13],
                [7,12],[7,11],[8,11],[8,13],[9,12],
                [11,12],[12,11],[12,13],[13,12],

            ]
            .forEach((point) => {
                this.cells[point[0]][point[1]] = 1;
            });   

            // Oscillators
            [

            ]
            .forEach((point) => {
                this.cells[point[0]][point[1]] = 1;
            });
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
                if(this.continue) this.update();
            }, 1);
        },
        stop(){
            this.continue = false;
        },
        start(){
            this.continue = true;
            this.update();
        }
    },
    destroyed() {
        this.continue = false
    }
}

</script>
<style scoped>
canvas {    
    width: 100%;
}
</style>

