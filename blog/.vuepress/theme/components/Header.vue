<template>
  <header class="site-header" id="large-header">
    <canvas id="canvas">Oh no! Your browser doesn't support canvas!</canvas>

    <div :class="`animation-toggler ${animateHeader ? 'top-1' : 'top-2'}`">
      <a
        href="javascript:void(0)"
        @click="animateHeader = !animateHeader"
      >{{ animateHeader ? 'Выкл!' : 'Вкл!' }}</a>
    </div>

    <div class="page-title" v-if="$page.title" :style="{opacity: animateHeader ? 0 : 1}">{{ $page.title }}</div>

    <div v-else class="main-title" :style="{opacity: animateHeader ? 0 : 1}">
      <router-link to="/" class="site-title">{{ $site.title }}</router-link>
      <sup>
        <router-link to="/about" class="page-link">Что это?</router-link>
      </sup>
      <Socials />
    </div>
  </header>
</template>

<script>
import Socials from "./Socials";
import Vue from 'vue'

//todo: https://www.youtube.com/watch?v=PWjIeJDE7Rc

export default {
  components: {
    Socials,
  },
  data: function () {
    return {
      animateHeader: true,
      gl: null,
      raf: window.requestAnimationFrame,
      craf: window.cancelAnimationFrame,
      numOfParticles: 0
    };
  },
  computed: {
    title() {
      return this.$page.title;
    },
    path() {
      return this.$page.path;
    }
  },
  mounted() {
    let canvas =
      "OffscreenCanvas" in window
        ? document.getElementById("canvas").transferControlToOffscreen()
        : document.getElementById("canvas");
    this.gl = canvas.getContext("webgl2");
    if (this.gl === null) {
      alert("Unable to initialize WebGL. Your browser or machine may not support it.");
      return;
    }
    // Main
    let {width, height} = this.setHeaderDimensions(canvas);
    this.initParticles(width, height);

    window.addEventListener("scroll", this.stopAnimationWhenScrolledOut);
    window.addEventListener("resize", this.setHeaderDimensions);

    // function drawConnections() {
    //   // !!! uncomment these in case if you have a good GPU cooler
    //   this.gl.globalCompositeOperation = "source-over";
    //   this.gl.fillStyle = "rgba(1, 1, 1, 0.2)";
    //   this.gl.globalCompositeOperation = "lighter";
    //
    //   for (let i = 0; i < particles.length; i++) {
    //     const p = particles[i];
    //     this.gl.beginPath();
    //
    //     for (let n = 0; n < particles.length; n++) {
    //       const p2 = particles[n];
    //       const yd = p2.location.y - p.location.y;
    //       const xd = p2.location.x - p.location.x;
    //       const distance = Math.sqrt(xd * xd + yd * yd);
    //
    //       if (distance < 140) {
    //         this.gl.lineWidth = 1;
    //         this.gl.moveTo(p.location.x, p.location.y);
    //         this.gl.lineTo(p2.location.x, p2.location.y);
    //       }
    //     }
    //     this.gl.strokeStyle = p.rgba;
    //     this.gl.stroke();
    //
    //     p.location.x =
    //       p.location.x + p.speed * Math.cos((p.angle * Math.PI) / 180);
    //     p.location.y =
    //       p.location.y + p.speed * Math.sin((p.angle * Math.PI) / 180);
    //
    //     if (p.location.x < 0) p.location.x = width + 50;
    //     if (p.location.x > width + 50) p.location.x = 0;
    //     if (p.location.y < 0) p.location.y = height + 50;
    //     if (p.location.y > height + 50) p.location.y = 0;
    //   }
    // }
  },
  methods: {
    setHeaderDimensions(canvas) {
      let largeHeader = document.getElementById("large-header");
      let width;
      let height;

      width = window.innerWidth;
      height = window.innerWidth / (this.path === "/" ? 2 : 4);

      largeHeader.style.height = height + "px";
      largeHeader.style.width = width + "px";
      largeHeader.style.overflow = "hidden";

      canvas.width = width;
      canvas.height = height;

      this.gl.viewport(0, 0, canvas.width, canvas.height);
      this.gl.clearColor(0.0, 0.0, 0.0, 1.0);
      this.gl.clear(this.gl.COLOR_BUFFER_BIT);

      return {width, height}
    },

    stopAnimationWhenScrolledOut() {
      this.animateHeader =
          this.animateHeader && document.documentElement.scrollTop < height / 2;
    },

    initParticles(w, h) {
      let particles = new Array(this.numOfParticles || Math.floor(h / 5)).fill().map((_) => {
        return this.newRandomParticle(w, h)
      });

      // drawConnections()
      console.table(particles);
      return particles;
    },

    randomRGBA() {
      const r = Math.round(Math.random() * 255);
      const g = Math.round(Math.random() * 255);
      const b = Math.round(Math.random() * 255);
      const a = Math.random() * 0.5;
      return "rgba(" + r + ", " + g + "," + b + ", " + a + ")";
    },

    newRandomParticle(w, h) {
      return {
        locationX: Math.random() * w,
        locationY: Math.random() * h,
        speed: Math.random(),
        angle: Math.random() * 360,
        color: this.randomRGBA()
      }
    }
  }
};
</script>

<style lang="stylus">
.site-header {

  .fullscreen {
    position:fixed;
    left:0;
    top:0;
    width:100%;
    height:100%;
  }

  position: relative;
  background: #111;
  background-size: cover;
  background-position: center center;
  z-index: 1;
  .animation-toggler {
    position: absolute;
    right: 3em;

    a {
      color: yellow;
      font-size: 0.825em;
      text-decoration: none;
      border-bottom: 1px dotted pink;
    }

    &.top-1 {
      top: 1em;
    }

    &.top-2 {
      top: 2em;
    }
  }

  .page-title {
    color: white;
    font-size: calc(1.3em + 3vw);
    letter-spacing: -1px;
    position: absolute;
    margin: 0;
    padding: 0;
    text-align: center;
    top: 50%;
    left: 50%;
    -webkit-transform: translate3d(-50%, -50%, 0);
    transform: translate3d(-50%, -50%, 0);
    //transition: opacity 2s ease-in-out 1s;
    transition: transform 2s ease-in-out 1s;

  }

  .main-title {
    font-size: 5vw;
    letter-spacing: -1px;
    line-height: 1.75;
    position: absolute;
    margin: 0;
    padding: 0;
    text-align: center;
    top: 50%;
    left: 50%;
    -webkit-transform: translate3d(-50%, -50%, 0);
    transform: translate3d(-50%, -50%, 0);
    transition: opacity 2s ease-in-out 1s;
    sup {
      top: -2.25em;
      font-size: 0.25em;

      a {
        color: pink;

        &:hover {
          color: #fdfdfd;
        }
      }
    }

    a {
      color: #fdfdfd;
      text-decoration: none;
    }

    a:hover {
      color: pink;
    }
  }
}
</style>

