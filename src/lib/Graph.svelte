<script lang="ts">
    import { getContext, onDestroy, onMount } from "svelte";
    import type { CanvasContext } from "../types";
    const canvasContext = getContext("canvas") as CanvasContext;
</script>