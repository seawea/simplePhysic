<script lang="ts">
  import { getContext, onDestroy, onMount } from "svelte";
  import type {CanvasContext} from "../types";
  let canvasContext = getContext("canvas") as CanvasContext;

  type CTX = CanvasRenderingContext2D;

  export let set: {
      display: boolean,
      radius: number,
      onMove: boolean,
      x: number,
      y: number,
      start: boolean
  }
  export let mp: {
      x: number,
      y: number
  }

  let dt=1/60;
  $: g=10*set.radius/15;
  let v:number;
  let vy:number;


  let dotArr: [number] = [-100]
  let count = 0

  let shadowDot: [[number,number]] = [[-100, 20]]
  let shadowHis: [[number,number]] = [[-100, 20]]

  onMount(() => {
    canvasContext.addDrawFn(draw);
  });

  onDestroy(() => {
    canvasContext.removeDrawFn(draw);
  });

  function draw(ctx: CTX) {
      if (set.display){
          document.getElementById("gunForm")!.style.display = "block"

          if (set.start){
              run()
              set.start=false
          }

          floor(ctx)
          aim(ctx)
          dotDraw(ctx)
          shadow(set.x, set.y, ctx)


          if (set.onMove) {
              ctx.beginPath()
              ctx.arc(set.x, set.y, set.radius, 360, 0, 2 * Math.PI)
              ctx.fill()

              update()


              if(set.y>400){
                  set.onMove=false
                  addDot(set.x)
              }


          }

      }
      else {
          document.getElementById("gunForm")!.style.display = "none"
      }
  }
  function update(){
      set.x+=v;
      set.y-=vy;
      vy-=g*dt;
  }

  function floor(ctx: CTX) {
      ctx.beginPath();
      ctx.moveTo(0, 400);
      ctx.lineTo(1500, 400);
      ctx.stroke();
  }

  function aim(ctx: CTX) {
      ctx.beginPath();
      ctx.moveTo(20, 400);
      if (mp.y>400){
          mp.y=400
      }

      ctx.lineTo(mp.x,mp.y);
      ctx.stroke();
  }

  function run(){
      if (!set.onMove) {
          set.onMove=true
          vy=(400-mp.y)/20
          set.y = 400
          set.x = 20
          v=(mp.x-20)/25
          shadowHis=shadowDot
          shadowDot=[[-100, 20]]
      }
  }

  function shadow(x:number ,y:number, ctx: CTX) {
      shadowDot.push([x,y])
      for (let i of shadowDot ){
          ctx.beginPath()
          ctx.arc(i[0],i[1],1,360, 0,2 * Math.PI)
          ctx.fill()
      }
      for (let i of shadowHis ){
          ctx.beginPath()
          ctx.arc(i[0],i[1],1,360, 0,2 * Math.PI)
          ctx.fill()
      }
  }

  function addDot(x: number) {
      if (dotArr.length < 10 ) {
          dotArr.push(x)
      }
      else {
          if (count > 9){count=0}
          dotArr[count] = x
          count = count+1
      }

  }

  function dotClear() {
      dotArr = [-100]

      shadowDot = [[0, -20]]
      shadowHis = [[0, -20]]
  }

  function dotDraw(ctx: CTX) {
      for (let i of dotArr ){
          ctx.beginPath()
          ctx.arc(i,400,5,360, 0,2 * Math.PI)
          ctx.fill()
      }
  }

</script>

<div id="gunForm">
    <label>
        <label for="m">Масса</label>
        <input id="m" type="number"
               min="30" max="50"
               bind:value={set.radius}/>
    </label>
    <button on:click={dotClear}>
        Очистить
    </button>
</div>

<style>
    div {
        display: none;
    }
</style>