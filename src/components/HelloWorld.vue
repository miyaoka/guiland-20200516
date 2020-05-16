<template>
  <div>
    <h1>#土曜GUI</h1>
    <h2>時計widget作りたい</h2>

    <div class="bezel">ベゼル（枠）</div>
    <div class="dial">文字盤</div>
    <div class="hourHand">長針</div>
    <div class="minuteHand">短針</div>
    <div class="secondHand">秒針</div>

    <section class="info">
      <div class="now">{{ now }}</div>
      <p class="vals">
        {{ hourVal.toFixed(2) }}時 {{ minVal.toFixed(2) }}分
        {{ secVal.toFixed(2) }}秒
      </p>
    </section>

    <section class="control">
      <label>
        radius
        <input v-model.number="radius" type="range" min="1" max="500" />
      </label>
    </section>

    <div class="container">
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
      <svg :height="diameter" :width="diameter">
        <circle
          :cx="radius"
          :cy="radius"
          :r="radius * 0.95"
          stroke="black"
          stroke-width="2"
          fill="#f8f8f8"
        />
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
            :x2="radius * 0.9"
            y2="0"
            stroke="black"
            :stroke-width="radius * 0.03"
            :style="minStyle"
          />
          <line
            x1="0"
            y1="0"
            :x2="radius * 0.8"
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

const dialToRadian = (dialIndex: number) => ((3 - dialIndex) / 6) * Math.PI
const PI2 = Math.PI * 2

export default Vue.extend({
  data() {
    return {
      radius: 200,
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
  mounted() {
    this.update()
  },
  methods: {
    getTextStyle(i: number) {
      const x = Math.cos(dialToRadian(i)) * this.radius * 0.85
      const y = -Math.sin(dialToRadian(i)) * this.radius * 0.85
      return {
        left: `${x}px`,
        top: `${y}px`,
        fontSize: `${this.radius * 0.15}px`,
        // transform: `translate(${x}px, ${y}px)`,
      }
    },
    getDialX(i: number) {
      return Math.cos(dialToRadian(i)) * this.radius * 0.9
    },
    getDialY(i: number) {
      return -Math.sin(dialToRadian(i)) * this.radius * 0.9
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
  border: 1px solid #f00;
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
