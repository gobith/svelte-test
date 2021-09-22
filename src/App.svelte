<script>
  import { onMount } from "svelte";
  import paths from "./path";
  import areas from "./areas";

  paths.forEach((path) => {
    path["path"] = new Path2D(path.d);
  });

  $: selectedPaths = [];

  areas.forEach((area) => {
    area["path"] = new Path2D(area.d);
  });

  onMount(() => {
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");

    const mousemove = (event) => {
      ctx.strokeStyle = "red";
      ctx.lineWidth = 4;

      paths.forEach((path) => {
        if (ctx.isPointInPath(path.path, event.offsetX, event.offsetY)) {
          ctx.stroke(path.path);
          console.log(path.id);
          path.selected = true;
        }
      });
      selectedPaths = paths.filter((path) => {
        return path.selected;
      });
    };

    const keydown = (event) => {
      console.log(event);
      if (!event.keyCode == 32) {
        return;
      }

      let innerText = document.getElementById("input").innerText;
      console.log(innerText);
      copyToClipboard(`; add: (GeoBRArea new pathIds: { ${innerText} }; name: '')`);
      paths.forEach((path) => {
        path.selected = false;
      });
      draw();
    };

    const draw = () => {
      ctx.resetTransform();
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.setLineDash([10, 6]);
      ctx.scale(0.3, 0.3);
      ctx.lineWidth = 2;

      ctx.strokeStyle = "black";
      ctx.fillStyle = "gray";

      areas.forEach((area) => {
        ctx.fill(area.path);
      });

      paths.forEach((path) => {
        ctx.stroke(path.path);
      });
    };
    draw();

    canvas.addEventListener("mousemove", mousemove);
    document.addEventListener("keydown", keydown);

    const copyToClipboard = (str) => {
      const el = document.createElement("textarea");
      el.value = str;
      document.body.appendChild(el);
      el.select();
      document.execCommand("copy");
      document.body.removeChild(el);
    };
  });
</script>

<canvas id="canvas" width="550" height="1200" />
<div id="input">

  {#each selectedPaths as path}
    <span>{path.id} .</span>
  {/each}


</div>

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
