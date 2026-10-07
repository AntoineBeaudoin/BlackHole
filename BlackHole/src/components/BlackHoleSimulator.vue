<template>
  <canvas ref="canvas">
    
  </canvas>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";

const canvas = ref();

let ctx;
let animationId;
let particles = [];

const blackHole = {
  x: 0,
  y: 0,
  mass: 4000
};

const nbParticulesMax = 1000;

function resize() {
  canvas.value.width = window.innerWidth;
  canvas.value.height = window.innerHeight;

  blackHole.x = canvas.value.width / 2;
  blackHole.y = canvas.value.height / 2;
}

function createParticules() {
  particles = [];

  for (let i = 0; i < nbParticulesMax; i++) {
    const angle = Math.random() * Math.PI * 2;
    const radius = 250 + Math.random() * 350;

    particles.push({
      x: blackHole.x + Math.cos(angle) * radius,
      y: blackHole.y + Math.sin(angle) * radius,
      vx: -Math.sin(angle) * 1.5,
      vy: Math.cos(angle) * 1.5,
      size: Math.random() * 2 + 0.5
    });
  }
}

function update() {
  ctx.fillStyle = "rgba(0,0,0,0.15)";
  ctx.fillRect(0, 0, canvas.value.width, canvas.value.height);

  for (const p of particles) {
    const dx = blackHole.x - p.x;
    const dy = blackHole.y - p.y;

    const distSq = dx * dx + dy * dy;
    const dist = Math.sqrt(distSq);

    if (dist < 12) {
      const angle = Math.random() * Math.PI * 2;
      const radius = 350 + Math.random() * 300;

      p.x = blackHole.x + Math.cos(angle) * radius;
      p.y = blackHole.y + Math.sin(angle) * radius;
      p.vx = -Math.sin(angle) * 1.5;
      p.vy = Math.cos(angle) * 1.5;
    }

    const force = blackHole.mass / distSq;

    p.vx += (dx / dist) * force;
    p.vy += (dy / dist) * force;

    p.x += p.vx;
    p.y += p.vy;

    ctx.beginPath();
    ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
    ctx.fillStyle = "white";
    ctx.fill();
  }

  ctx.beginPath();
  ctx.arc(blackHole.x, blackHole.y, 18, 0, Math.PI * 2);
  ctx.fillStyle = "black";
  ctx.fill();

  ctx.strokeStyle = "orange";
  ctx.lineWidth = 5;
  ctx.beginPath();
  ctx.arc(blackHole.x, blackHole.y, 25, 0, Math.PI * 2);
  ctx.stroke();

  animationId = requestAnimationFrame(update);
}

onMounted(() => {
  ctx = canvas.value.getContext("2d");

  resize();
  createParticules();

  window.addEventListener("resize", resize);

  ctx.fillStyle = "black";
  ctx.fillRect(0, 0, canvas.value.width, canvas.value.height);

  update();
});

onUnmounted(() => {
  cancelAnimationFrame(animationId);
  window.removeEventListener("resize", resize);
});
</script>

<style scoped>
canvas {
  display: block;
  width: 100vw;
  height: 100vh;
  background: black;
}
</style>