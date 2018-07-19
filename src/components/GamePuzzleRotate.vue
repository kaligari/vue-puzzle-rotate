<template>
    <div>
        <div class="columns is-desktop" id="game-puzzle-rotate">
            <div class="column">
                <div class="puzzle" :class="{'done':done}" :style="{width:width+'px',height:height+'px',backgroundImage:'url('+image+')',backgroundSize:width+'px '+height+'px'}">
                    <div class="row" v-for="(row,rowIdx) in pzls">
                        <div class="pzl" v-for="(col,colIdx) in row" @click="pzzlClick(rowIdx,colIdx)" :class="{'clicked':col.clicked}" :style="{width:(width/size)+'px',height:(height/size)+'px',backgroundImage:'url('+image+')',backgroundSize:width+'px '+height+'px',backgroundPosition:(col.col*-(width/size))+'px '+(col.row*-(height/size))+'px',transform:'rotate('+(col.rotate*90)+'deg)'}"></div>
                    </div>
                </div>
            </div>
            <div class="column">
                <img :src="image" alt="" :style="{width:width+'px',height:height+'px'}">
            </div>
        </div>
        <div class="columns">
            <div class="column has-text-centered timeColumn">
                <b>Time:</b>
                <br />
                {{ time }} s.
            </div>
            <transition name="fade">
                <div class="modal is-active" v-if="modal">
                    <div class="modal-background"></div>
                    <div class="modal-content">
                        <div class="box has-text-centered">
                            <p>Your time is {{ time }} s.</p><br />
                            <br />
                            <button type="button" class="button is-primary" @click="modal = false">Zamknij</button>
                        </div>
                    </div>
                    <button class="modal-close is-large" aria-label="close" @click="modal = false"></button>
                </div>
            </transition>
        </div>
    </div>
</template>

<script>
export default {
    name: 'GamePuzzleRotate',
    data: function(){
        return{
            time: 0,
            test: 'Testtt',
            timeInterval: null,
            pzls: 0,
            done: false,
            selected: 0,
            width:500,
            height:500,
            modal: false,
        }
    },
    props: {
        image: {
            type: String,
            required: true,
        },
        size: {
            default: 3,
        }
    },
    methods: {
        pzzlClick(x,y){
            // zaznacz że kliknięty
            // this.pzls[x][y].rotate = this.pzls[x][y].rotate >= 3 ? 0 : this.pzls[x][y].rotate+1;
            this.pzls[x][y].rotate++;

            // sprawdź czy sa ustawione
            let done = true;
            for(let i = 0; i < this.size; i++){
                for(let j = 0; j < this.size; j++){
                    if(this.pzls[i][j].rotate % 4 != 0) done = false;
                    console.log(done);
                }
            }
            if(done){
                this.done = true;
                // zawibruj jesli telefon
                // window.navigator.vibrate(800);
                clearInterval(this.timeInterval);
                setTimeout(()=>{
                    this.modal = true;
                },1000);
            }
        },
        changeGameSize() {
            window.onresize = (event) => {
                let bodyWidth = window.innerWidth || document.body.clientWidth;
                if(bodyWidth < 768) {
                    this.width = 300;
                    this.height = 300;
                } else {
                    this.width = 500;
                    this.height = 500;
                }
            };
        }
    },
    created(){
        // zainicjuj tablicę
        this.pzls = [];
        // pętla wierszy
        for(let x = 0; x < this.size; x++){
            // zainicjuj tymczasow tablicę elementów wiersza
            let row = [];
            // pętla kolumn
            for(let y = 0; y < this.size; y++){
                // wypełnij tablicę wiersza danymi o obrazku
                row.push({
                    row: x,
                    col: y,
                    rotate: Math.floor(Math.random() * 4),
                });
            }
            // dodaj tymczasowa listę wiersza do tablicy wszystkich bloków
            this.pzls.push(row);
        }
        // rozpocznij liczenie czasu
        this.timeInterval = setInterval(()=>{
            this.time++;
        },1000);
        // set initial game width and height
        if((window.innerWidth || document.body.clientWidth) < 768) {
            this.width = 300;
            this.height = 300;
        }
        // changing the game widh and height on resize
        this.changeGameSize();
    }
}



</script>

<style lang="less">
.fade-enter-active, .fade-leave-active {
    transition: opacity .5s
}
.fade-enter, .fade-leave-to{
    opacity: 0
}
#game-puzzle-rotate{
    text-align: center;
    img{
        display: inline-block;
        border:1px solid #000;
    }
    .puzzle{
        display: inline-block;
        border:1px solid #000;
        background-position: -1000px -1000px;
        background-repeat: no-repeat;
        &.done{
            background-position:0 0;
            .row{
                opacity: 0;
            }
        }
        .row{
            display: table-row;
            transition: opacity 500ms;
            .pzl{
                // border:1px solid #000;
                display: table-cell;
                transition: transform 300ms;
                &.clicked{
                    opacity: 0.5;
                }
            }
        }
    }

}
.timeColumn {
    font-size: 30px;
    line-height: 35px;
}

</style>
