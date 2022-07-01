<template>
  <div>
    <canvas ref="canvas" :width="canvasWidth" :height="canvasHeight" />
  </div>
</template>

<script>
import { ref, onMounted } from "vue";

export default {
  name: "HomeView",
  setup() {
    let canvas = ref(null);
    let context = null;
    let canvasWidth = ref(null);
    let canvasHeight = ref(null);
    let nodes = [];
    let edges = [];
    let selectedNode = null;

    const resizeCanvas = () => {
      canvasWidth.value = window.innerWidth;
      canvasHeight.value = window.innerHeight;
    };

    const drawCanvas = () => {
      context.clearRect(0, 0, canvasWidth.value, canvasHeight.value);

      // Box width
      var bw = canvasWidth.value;
      // Box height
      var bh = canvasHeight.value;
      // Padding
      var p = 0;

      for (var x = 0; x <= bw; x += 20) {
        context.moveTo(0.5 + x + p, p);
        context.lineTo(0.5 + x + p, bh + p);
      }

      for (var y = 0; y <= bh; y += 20) {
        context.moveTo(p, 0.5 + y + p);
        context.lineTo(bw + p, 0.5 + y + p);
      }
      context.strokeStyle = "#2F4F4F44";
      context.stroke();

      for (const edge of edges) {
        let fromNode = edge.from;
        let toNode = edge.to;
        context.beginPath();
        context.strokeStyle = fromNode.strokeStyle;
        context.moveTo(fromNode.x, fromNode.y);
        context.lineTo(toNode.x, toNode.y);
        context.stroke();
      }

      for (const node of nodes) {
        context.beginPath();
        context.fillStyle = node.selected
          ? node.fillStyle + "66"
          : node.fillStyle;
        context.arc(node.x, node.y, node.radius, 0, Math.PI * 2, true);
        context.strokeStyle = node.strokeStyle;
        context.fill();
        context.stroke();
      }
    };

    const handleDoubleClick = (e) => {
      let node = {
        x: e.x,
        y: e.y,
        radius: 40,
        fillStyle: "#F08080",
        strokeStyle: "#000",
        selected: false,
      };
      nodes.push(node);
      drawCanvas();
    };

    const withinNode = (x, y) => {
      return nodes.find((n) => {
        return (
          x > n.x - n.radius &&
          y > n.y - n.radius &&
          x < n.x + n.radius &&
          y < n.y + n.radius
        );
      });
    };

    const handleMouseMove = (e) => {
      if (selectedNode && e.buttons) {
        selectedNode.x = e.x;
        selectedNode.y = e.y;
        drawCanvas();
      }
    };

    const handleMouseDown = (e) => {
      let target = withinNode(e.x, e.y);
      if (selectedNode && selectedNode.selected) {
        selectedNode.selected = false;
      }
      if (target) {
        if (selectedNode && selectedNode !== target) {
          edges.push({ from: selectedNode, to: target });
        }
        selectedNode = target;
        selectedNode.selected = true;
        drawCanvas();
      }
    };

    const handleMouseUp = () => {
      if (selectedNode && !selectedNode.selected) {
        selectedNode = null;
      }
      drawCanvas();
    };

    // MOUNTED LOGIC
    onMounted(() => {
      context = canvas.value.getContext("2d");
      if (context) {
        resizeCanvas();

        window.addEventListener("dblclick", handleDoubleClick);
        window.addEventListener("mousedown", handleMouseDown);
        window.addEventListener("mouseup", handleMouseUp);
        window.addEventListener("mousemove", handleMouseMove);

        drawCanvas();
      }
    });

    return {
      canvas,
      context,
      canvasWidth,
      canvasHeight,
    };
  },
};
</script>

<style>
html,
body,
nav,
ul,
h1,
h2,
h3,
h4,
a,
canvas {
  margin: 0px;
  padding: 0px;
  color: var(--text-color);
}

html,
body {
  font-family: Roboto, -apple-system, BlinkMacSystemFont, "Segoe UI", Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  font-size: var(--root-font-size);
  background: var(--bg);
  height: 100%;
  width: 100%;
  overflow: hidden;
}

*,
body,
button,
input,
select,
textarea,
canvas {
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  outline: 0;
}
</style>
