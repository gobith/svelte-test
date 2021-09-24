<script>
  import { onMount } from "svelte";
  import paths from "./path";
  import areas from "./areas";
  import grid from "./grid";

  paths.forEach((path) => {
    path["path"] = new Path2D(path.d);
  });

  $: selectedPaths = [];

  areas.forEach((area) => {
    area["path"] = new Path2D(area.d);
  });

  grid.areaDots.forEach((areaDots) => {
    let isFirst = true;
    let path2D = new Path2D();

    areaDots.nodes.forEach((dot) => {
      if (isFirst) {
        path2D.moveTo(dot.x, dot.y);
        isFirst = false;
      } else {
        path2D.lineTo(dot.x, dot.y);
      }
    });
    areaDots["path"] = path2D;
  });

  console.log(grid.areaDots);

  grid.areas.forEach((area) => {
    area["path"] = new Path2D(area.d)
  });

  onMount(() => {
    const draw = () => {
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");
      ctx.resetTransform();
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.setLineDash([10, 6]);
      ctx.scale(1, 1);
      ctx.lineWidth = 2;

      ctx.strokeStyle = "black";
      ctx.fillStyle = "gray";

      // areas.forEach((area) => {
      //   ctx.fill(area.path);
      // });

      paths.forEach((path) => {
        ctx.stroke(path.path);
      });
    };

    const drawGrid = () => {
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");
      ctx.resetTransform();
      ctx.scale(1, 1);
      ctx.setLineDash([0, 0]);
      ctx.lineWidth = 2;
      ctx.strokeStyle = "green";
      ctx.fillStyle = "hsla(0, 100%, 70%, 0.3)";

      grid.areaDots.forEach((areaDots) => {
        ctx.fill(areaDots.path);
        //ctx.stroke();
      });

      ctx.lineWidth = 2;
      ctx.strokeStyle = "blue";

      grid.lines.forEach((line) => {
        ctx.fillStyle = "blue";
        ctx.beginPath();
        ctx.moveTo(line.a.x, line.a.y);
        ctx.lineTo(line.b.x, line.b.y);
        ctx.stroke();

        ctx.beginPath();
        ctx.arc(line.center.x, line.center.y, 12, 0, 2 * Math.PI);
        ctx.fill();
        ctx.fillStyle = "white";
        ctx.beginPath();
        ctx.fillText(line.id, line.center.x - 6, line.center.y + 4);
      });

      grid.dots.forEach((dot) => {
        ctx.fillStyle = "green";
        ctx.beginPath();
        ctx.arc(dot.x, dot.y, 10, 0, 2 * Math.PI);
        ctx.fill();
        ctx.fillStyle = "black";
        ctx.beginPath();
        ctx.fillText(dot.id, dot.x, dot.y + 4);
        ctx.fill();
      });
    };

    draw();
    //drawGrid();

    const mouseclick = (event) => {
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");
      ctx.fillStyle = "hsla(0, 100%, 70%, 0.3)";
      grid.areas.forEach((areaDots) => {
        if (ctx.isPointInPath(areaDots.path, event.offsetX, event.offsetY)) {
          console.log(areaDots.ids);
          ctx.fill(areaDots.path)
        }
      });
    };

    canvas.addEventListener("click", mouseclick);
  });
</script>

<canvas id="canvas" width="10000" height="10000" />
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
