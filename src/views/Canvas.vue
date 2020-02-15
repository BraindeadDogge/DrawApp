<template>
<div class="canvas-container">
    <canvas @click="canvasClicked" class="my-canvas" id="mycanvas" width="600" height="600"></canvas>
    <div>
    <button class="button">Line</button>
    <br/>
    <button class="button">Triangle</button>
    <br/>
    <div class="button">{{wiheight}}</div>
    </div>
</div>
</template>
<style lang="scss">
    .canvas-container {
        display: flex;
    }
    .button{
        width: 70px;
        height: 50px;
        margin-top: 10px;
        background-color: rgb(250, 174, 174);
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .my-canvas {
        width: 600px;
        height: 600px;
    }
</style>
<script>
export default {
    data(){
        return {
            canvas: null,
            points: [],
            wiheight: 10,
        }
    },
    methods:{
        clearCanvas() {
            const ctx = this.canvas.getContext("2d");
            ctx.fillStyle = 'white'
            ctx.fillRect(0,0,600,600)
            ctx.fill()
        },
        loadPoints(){
            this.$axios.get('http://188.225.47.187/api/canvas/points.php').then(response=>{
                console.log('response', response)
                this.points = response.data.filter((el) => el.x !== null && el.y !== null)
            })
        },
        drawPoint(point){
            const {x, y, color} = point
            const ctx = this.canvas.getContext("2d");
            ctx.fillStyle = color;
            ctx.fillRect(x - this.wiheight / 2, y - this.wiheight / 2, this.wiheight, this.wiheight);
            ctx.fill();
            // ctx.arc(x, y, radius, 0, 2 * Math.PI, false);
      
        },
        canvasClicked(e){
            this.$axios.get('http://188.225.47.187/api/canvas/setpoint.php', {
                params: {
                    x: e.pageX,
                    y: e.pageY
                }
            })
        },
    },
    mounted(){
        this.canvas= document.getElementById('mycanvas')
        setInterval(()=>{
            this.loadPoints()
        }, 4000)
    },
    watch: {
        points(){
            this.clearCanvas()
            for (const point of this.points) {
                this.drawPoint(point)
            }
        }
    }
}
</script>