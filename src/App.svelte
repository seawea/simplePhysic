<script lang="ts">
    import Canvas from "./lib/Canvas.svelte";
    import Ball from "./lib/Ball.svelte";
    import Gun from "./lib/Gun.svelte";


    let mathSet = {
        display: true,
        radius: 20,
        len: 200,
        g: 980,
        bob: {
            phi: Math.PI * 0.2,
            v: 0,
            a: 0,
        }
    }

    let gunSet = {
        display: false,
        radius: 20,
        onMove: false,
        x: 60,
        y: 400,
        start: false
    }
    let mousePos = {x: 50, y: 400}
    let mouseDown = false

    let names = {
        "Нитяной маятник": mathSet,
        "Снаряд": gunSet
    }

    let actual = mathSet;
    function sideButton(param: any) {
        actual.display = false
        param.display = !param.display
        actual = param
    }

    function mousePosition (e: any) {
        let rect = e.target.getBoundingClientRect()
        mousePos.x = e.clientX - rect.left
        mousePos.y = e.clientY - rect.top
    }

</script>


<main>
    <div id="sidebar">
        {#each Object.entries(names) as [name, param]}
            <div>
                <button id="p" on:click={() => sideButton(param)}>{name}</button>
            </div>
        {/each}
    </div>

    <div id="graph">
        <Canvas
                on:mousedown={(e)=> {
                    mousePosition(e)
                    mouseDown = true}}
                on:mousemove={(e) => {
                    if (mouseDown){mousePosition(e)}}}
                on:mouseup={() => {
                    mouseDown=false
                    gunSet.start=true}}>
            <Ball set={mathSet}/>
            <Gun set={gunSet} mp={mousePos}/>
        </Canvas>
    </div>

</main>


<style>
    * {
        padding: 0;
        margin: 0;
    }

    #sidebar {
        height: 98.5vh;
        width: 25vw;

        position: absolute;
        top: 0;
        left: 0;
    }

    #graph {
        height: 98.5vh;
        width: 74vw;

        position: absolute;
        top: 0;
        left: 25vw;
    }

    button {
        margin-bottom: 0.3em;
    }
</style>
