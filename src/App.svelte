<script>
  import { onMount } from "svelte";
  import paths from "./path";

  onMount(() => {

    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    ctx.scale(0.5 , 0.5)

    paths.forEach((path) => {
        ctx.stroke(new Path2D(path.d))
    })


  });
</script>

<canvas id="canvas" width=2000 height=2000 />

<style>
  :global(* body) {
    margin: 0px;
    padding: 0px;
    box-sizing: border-box;
  }
  :global(body) {
    height: 100vh;
    width: 100vw;
  
  }

  
</style>
