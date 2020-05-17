<template>
  <div>
    <section class="control">
      <label>
        radius
        <input v-model.number="radius" type="range" min="10" max="500" />
      </label>
    </section>

    <div class="container">
      <div class="caseOuter" :style="caseOuterStyle" />
      <div class="caseInner" :style="caseInnerStyle" />
      <div v-show="radius > 70" class="textContainer">
        <p
          v-for="(num, i) in dialNumbers"
          :key="i"
          class="dialText"
          :style="getTextStyle(i)"
        >
          {{ num }}
        </p>
      </div>
      <svg :height="diameter" :width="diameter" class="svg">
        <g :transform="`translate(${radius}, ${radius})`">
          <line
            x1="0"
            y1="0"
            :x2="radius * 0.5"
            y2="0"
            stroke="black"
            :stroke-width="radius * 0.05"
            :style="hourStyle"
          />
          <line
            x1="0"
            y1="0"
            :x2="radius * 0.85"
            y2="0"
            stroke="black"
            :stroke-width="radius * 0.03"
            :style="minStyle"
          />
          <line
            x1="0"
            y1="0"
            :x2="radius * 0.85"
            y2="0"
            stroke="black"
            :stroke-width="radius * 0.01"
            :style="secStyle"
          />
        </g>
      </svg>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { ipcRenderer } from 'electron'

const dialToRadian = (dialIndex: number) => ((3 - dialIndex) / 6) * Math.PI
const PI2 = Math.PI * 2

export default Vue.extend({
  data() {
    return {
      radius: 100,
      dialNumbers: [...new Array(12)].map((_, i) => ((i + 11) % 12) + 1),
      now: new Date(),
    }
  },
  computed: {
    hourVal(): number {
      return this.now.getHours() + this.minVal / 60
    },
    minVal(): number {
      return this.now.getMinutes() + this.secVal / 60
    },
    secVal(): number {
      return this.now.getSeconds() + this.now.getMilliseconds() / 1000
    },
    diameter(): number {
      return this.radius * 2
    },
    caseOuterStyle(): Record<string, string> {
      return {
        width: `${this.diameter}px`,
        height: `${this.diameter}px`,
      }
    },
    caseInnerStyle(): Record<string, string> {
      return {
        width: `${this.diameter * 0.92}px`,
        height: `${this.diameter * 0.92}px`,
      }
    },
    hourStyle(): Record<string, string> {
      const rad = ((3 - this.hourVal) / 12) * PI2
      return {
        transform: `rotate(${rad * -1}rad)`,
      }
    },
    minStyle(): Record<string, string> {
      const rad = ((15 - this.minVal) / 60) * PI2
      return {
        transform: `rotate(${rad * -1}rad)`,
      }
    },
    secStyle(): Record<string, string> {
      const rad = ((15 - this.secVal) / 60) * PI2
      return {
        transform: `rotate(${rad * -1}rad)`,
      }
    },
  },
  watch: {
    radius(val: number) {
      ipcRenderer.send('resize', val)
    },
  },
  mounted() {
    this.update()
  },
  methods: {
    getTextStyle(i: number) {
      const x = Math.cos(dialToRadian(i)) * this.radius * 0.8
      const y = -Math.sin(dialToRadian(i)) * this.radius * 0.8
      return {
        left: `${x}px`,
        top: `${y}px`,
        fontSize: `${this.radius * 0.15}px`,
        // transform: `translate(${x}px, ${y}px)`,
      }
    },
    getDialX(i: number) {
      return Math.cos(dialToRadian(i)) * this.radius * 0.85
    },
    getDialY(i: number) {
      return -Math.sin(dialToRadian(i)) * this.radius * 0.85
    },
    update() {
      this.now = new Date()
      requestAnimationFrame(this.update)
    },
  },
})
</script>

<style scoped lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans&display=swap');
.container {
  position: relative;

  .textContainer {
    margin: auto;
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    width: 0;
    height: 0;

    .dialText {
      font-family: 'Josefin Sans', sans-serif;
      font-size: 30px;
      position: absolute;
      margin: 0;
      padding: 0;
      transform: translate(-50%, -50%);
    }
  }
}

.caseOuter {
  background: linear-gradient(
    to right,
    rgba(243, 226, 199, 1) 0%,
    rgba(193, 158, 103, 1) 50%,
    rgba(182, 141, 76, 1) 51%,
    rgba(233, 212, 179, 1) 100%
  );
  box-shadow: 4px 4px 11px 0px rgba(50, 50, 50, 0.66);
  margin: auto;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  width: 0;
  z-index: -2;
  border-radius: 100%;
}
.caseInner {
  -webkit-app-region: drag;
  -webkit-user-select: none;
  background: #fff;
  margin: auto;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  width: 0;
  z-index: -1;
  border-radius: 100%;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
