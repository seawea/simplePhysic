<script lang="ts">
    import {onDestroy, onMount, setContext} from "svelte";
    import type {DrawFn, Point} from "../types";

    let frameId: number;
    let clearFrames = true;

    let canvasElement: HTMLCanvasElement;
    let fnsToDraw = [] as DrawFn[];

    let w = 600;
    let h = 500;



    setContext("canvas", {
        addDrawFn: (fn: DrawFn) => {
            fnsToDraw.push(fn);
        },
        removeDrawFn: (fn: DrawFn) => {
            let index = fnsToDraw.indexOf(fn);
            if (index > -1) {
                fnsToDraw.splice(index, 1);
            }
        },
    });

    onMount(() => {
        let ctx = canvasElement.getContext("2d");

        frameId = requestAnimationFrame(() => draw(ctx));
    });

    onDestroy(() => {
        if (frameId) {
            cancelAnimationFrame((frameId))
        }
    });

    function draw(ctx: CanvasRenderingContext2D) {
        if (clearFrames) {
            ctx.clearRect(0, 0, w, h)
        }
        {
            fnsToDraw.forEach((fn) => fn(ctx));

            frameId = requestAnimationFrame(() => draw(ctx));
        }
    }
</script>

<canvas width={w} height={h}
        on:mousedown
        on:mouseup
        on:mousemove
        bind:this={canvasElement}/>
<slot/>

<style>
    canvas {
        border: indianred solid;
    }
</style>