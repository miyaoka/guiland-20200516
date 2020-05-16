<template>
  <div>
    <h1>#土曜GUI</h1>
    <h2>時計widget作りたい</h2>

    <div class="bezel">ベゼル（枠）</div>
    <div class="dial">文字盤</div>
    <div class="hourHand">長針</div>
    <div class="minuteHand">短針</div>
    <div class="secondHand">秒針</div>
    <svg :height="diameter" :width="diameter">
      <circle
        :cx="radius"
        :cy="radius"
        :r="radius"
        stroke="black"
        stroke-width="3"
        fill="#ccc"
      />
      <g :transform="`translate(${radius}, ${radius})`">
        <text
          v-for="(num, i) in dialNumbers"
          :key="i"
          :x="getDialX(i)"
          :y="getDialY(i)"
          class="small"
        >
          {{ num }}
        </text>
      </g>
    </svg>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

const dialToRadian = (dialIndex: number) => ((3 - dialIndex) / 6) * Math.PI

export default Vue.extend({
  data() {
    return {
      radius: 200,
      dialNumbers: [...new Array(12)].map((_, i) => ((i + 11) % 12) + 1),
    }
  },
  computed: {
    diameter(): number {
      return this.radius * 2
    },
  },
  methods: {
    getDialX(i: number) {
      return Math.cos(dialToRadian(i)) * this.radius * 0.9
    },
    getDialY(i: number) {
      return -Math.sin(dialToRadian(i)) * this.radius * 0.9
    },
  },
})
</script>

<style scoped lang="scss">
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
