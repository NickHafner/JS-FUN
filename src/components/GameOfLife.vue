<template>
<div>
    <div class="canvas-container">
        <canvas id="game" :width="canvasWidth" :height="canvasHeight"></canvas>
    </div>
    <v-layout row align-center>       
        <v-flex md3 sm12 xs12 offset-md7>
            <br>Living Cells: {{displayCount}} <br>Generation: {{generation}}
            <v-overflow-btn
                v-model="selectedPattern"
                @change="patternChange"
                :items="patternTypes"
                label="Acorn"
                ></v-overflow-btn>
        </v-flex>
    </v-layout>
    <v-layout>
        <v-flex xs3 md6 offset-md8 offset-sm10 offset-xs9 >
            <v-btn color="info" @click="reset" right>Reset</v-btn>  
            <v-btn :color="startColor" @click="toggle" right>{{goOrStop}}</v-btn>
        </v-flex>
    </v-layout>
</div>
    
</template>
<script>
export default {
    name: 'GameOfLife',
    data() {
        return {
            cells: [],
            selectedPattern: "Acorn",
            continue: false,
            canvas: null,
            canvasWidth: 812,
            canvasHeight: 812,
            gameWidth: 124,
            gameHeight: 124,
            currentPattern: "Acorn",
            startColor: "success",
            goOrStop: 'Go',
            generation: 0,
            liveCells: 0,
            displayCount: 0,
            patternTypes: [
                { text: 'Still Life'},
                { text: 'Acorn'},
                { text: 'Glider Gun'},
                { text: 'Diehard'},
                { text: 'R-Pentomino'},
                { text: 'Random'},
            ]
        }
    },
    mounted() {
        this.canvas = document.getElementById('game').getContext("2d");
        this.canvas.strokeStyle = "#e1e1e1";
        this.canvas.fillStyle = "cadetblue";
        this.setUpCanvas();
        this.fillCanvasSet_Acorn();
    },
    methods: {
        setUpCanvas() {
            this.liveCells = 0;
            this.generation = 0;
            for (let i=0; i<this.gameWidth; i++) {
                this.cells[i] = [];
                for (let j=0; j<this.gameHeight; j++) {
                    this.cells[i][j] = 0;
                }
            }
        },
        fillCanvasRandom(){
            this.setUpCanvas();
            
            this.cells.forEach((row, x) => {
                row.forEach((cell, y) => {
                    if (Math.random() >= 0.5){
                        this.cells[x][y] = 1;
                        this.liveCells++;
                    }
                });
            });
            this.draw();
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
                this.liveCells++;
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
                this.liveCells++;
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
                this.liveCells++
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
                this.liveCells++;
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
                this.liveCells++;
            });   
            this.displayCount = this.liveCells;
            // TODO: Oscillators
            // [

            // ]
            // .forEach((point) => {
            //     this.cells[point[0]][point[1]] = 1;
            // });
            
            this.draw();
        },        
        update() { 
            let result = [];
            this.generation++;
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
            this.liveCells = 0;
            this.canvas.clearRect(0, 0, this.canvasWidth, this.canvasHeight);
            this.cells.forEach((row, x) => {
                row.forEach((cell, y) => {
                    this.canvas.beginPath();
                    this.canvas.rect(x*7, y*7, 7, 7);
                    if (cell) {
                        this.liveCells++;
                        this.canvas.fill();
                    } else {
                        this.canvas.stroke();
                    }
                });
            });
            this.displayCount = this.liveCells;
            setTimeout(() => {
                if(this.continue) this.update();
            }, 1);
        },
        toggle(){
            if(!this.continue){
                this.continue = true;
                this.goOrStop = 'Stop';
                this.startColor = 'error';
            }
            else {
                this.continue = false;
                this.goOrStop = 'Go';
                this.startColor = 'success';
            }
            this.draw();
        },
        destroyed() {
            this.continue = false;
        },
        reset(){
            this.patternChange(this.selectedPattern);
        },
        patternChange(e) {
            this.continue = false;
            this.goOrStop = 'Go';
            this.startColor = 'success';
            this.currentPattern = "Random";
            switch(e){
                case 'Acorn':
                    this.fillCanvasSet_Acorn();
                    break;
                case 'Diehard':
                    this.fillCanvasSet_Diehard();
                    break;
                case 'Still Life':
                    this.fillCanvasSet_StillLife();
                    break;
                case 'R-Pentomino':
                    this.fillCanvasSet_R_pentomino();
                    break;
                case 'Random':
                    this.fillCanvasRandom();
                    break;
                case 'Glider Gun':
                    this.fillCanvasSet_GliderGun();
                    break;
                default:
                    console.log('default');
            }
        }
    }
}

</script>
<style scoped>
canvas {    
    width: 100%;
}
.canvas-container {
  position: relative;
  max-width: 1024px;
  min-width: 320px;
  margin: 0 auto;
}
</style>

