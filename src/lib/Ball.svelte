<script lang="ts">
    import { getContext, onDestroy, onMount } from "svelte";
    import type { CanvasContext } from "../types";

    const canvasContext = getContext("canvas") as CanvasContext;

    export let set: {
        display: boolean,
        radius: number,
        len: number,
        g: number,
        bob: {
            phi: number,
            v: number,
            a: number,
        }
    }

    export let w = 600;

    let dt = 1 / 60;
    let newLen = 200;
    let newRadius = 20;

    onMount(() => {
        canvasContext.addDrawFn(draw);
    });
    onDestroy(() => {
        canvasContext.removeDrawFn(draw);
    });

    function draw(ctx: CanvasRenderingContext2D) {
        if (set.display) {
           document.getElementById("ballForm")!.style.display = "block";

            ctx.beginPath()
            ctx.arc(Math.sin(set.bob.phi) * set.len + w / 2, Math.cos(set.bob.phi) * set.len, set.radius, 0, 2 * Math.PI)
            ctx.fill()
            ctx.moveTo(w / 2, 0)
            ctx.lineTo(Math.sin(set.bob.phi) * set.len + w / 2, Math.cos(set.bob.phi) * set.len)
            ctx.stroke()

            update()
        }
        else {
            document.getElementById("ballForm")!.style.display = "none";
        }

    }
    function update() {
        set.bob.a = -(set.g / set.len) * Math.sin(set.bob.phi)
        set.bob.v += set.bob.a * dt
        set.bob.phi += set.bob.v * dt
    }

    function newSet() {
        set.len = newLen
        set.radius = newRadius
    }
</script>

<div id="ballForm">
    <label for="len">Длина нити</label>
    <input id="len" name="len" type="range"
           min="100" max="400"
           bind:value={newLen}/>

    <label for="mass">Масса шара</label>
    <input id="mass" name="mass" type="range"
           min="5" max="50"
           bind:value={newRadius}/>
    <button type="submit" on:click={newSet}>Применить</button>

</div>

<style>
    div {
        display: block;
    }
</style>


