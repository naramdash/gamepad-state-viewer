<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, onUnmounted, ref } from 'vue';

const gamepads = ref<(Gamepad | null)[]>([])
const gamepad0 = computed(() => gamepads.value[0])

function refreshGamepads() {
  gamepads.value = navigator.getGamepads()
}
onMounted(function withGamepadEvents() {

  refreshGamepads()
  window.addEventListener('gamepadconnected', refreshGamepads);
  window.addEventListener('gamepaddisconnected', refreshGamepads);

  onUnmounted(() => {
    window.removeEventListener('gamepadconnected', refreshGamepads);
    window.removeEventListener('gamepaddisconnected', refreshGamepads);
  })
})

onMounted(function withGamepadStatePolling() {
  let loopId = NaN
  function pollingLoop() {
    refreshGamepads()
    loopId = requestAnimationFrame(pollingLoop)
  }

  pollingLoop()
  onBeforeUnmount(() => cancelAnimationFrame(loopId))
})
</script>

<template>
  <h1>Gamepad API State viewer</h1>

  <p>
    This is a simple demonstration of the Gamepad API. It shows the state of the first gamepad connected to the system.
    <br />
    You can press buttons and move the axes of the gamepad to see the changes in the state.
    <br />
    I have tested this with an Xbox Elite controller + Ubuntu-24.04 (amd64) + Microsoft Edge v124 on 2024-05-07.
  </p>

  <strong> If gamepad is connected but shows as disconnected, try to press a button or move an axis. </strong>

  <p>
    For more information, see <a href="https://developer.mozilla.org/en-US/docs/Web/API/Gamepad_API" target="_blank">MDN
      Gamepad API
      documentation</a>.
    <br />
    Event listening for each button is not supported currently. See <a
      href="https://github.com/w3c/gamepad/blob/button-axis-events-explainer/button-axis-events-explainer.md"
      target="_blank">GamePad
      API Button and Axis Events Explainer</a>.

  </p>

  <h2> Visualization </h2>
  <p>
    Check <a href="https://w3c.github.io/gamepad/#remapping" target="_blank">W3C STANDARD GAMEPAD layout</a>.
  </p>
  <svg viewBox="0 0 800 400" width="800" height="400">
    <g data-comment="bodies">
      <rect class="body center" x="220" y="110" width="360" height="200" />
      <circle class="body left" r="100" cx="180" cy="200" />
      <circle class="body left-bottom" r="55" cx="260" cy="280" />
      <circle class="body right" r="100" cx="620" cy="200" />
      <circle class="body right-bottom" r="55" cx="540" cy="280" />
    </g>

    <text v-if="gamepad0" x="400" y="50" style="font-size: 20px; fill: cornflowerblue;">CONNECTED</text>
    <text v-else x="400" y="50" style="font-size: 20px; fill: grey">DISCONNECTED</text>

    <text x="400" y="100" style="font-size: 30px;">STANDARD GAMEPAD</text>

    <g data-comment="left side buttons">
      <rect class="button" :class="{ pressed: gamepad0?.buttons[6].pressed }" x="130" y="40" width="100" height="25"
        rx="10" />
      <text x="180" y="60">b[6]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[4].pressed }" x="130" y="70" width="100" height="25"
        rx="10" />
      <text x="180" y="90">b[4]</text>
    </g>

    <g data-comment="right side buttons">
      <rect class="button" :class="{ pressed: gamepad0?.buttons[7].pressed }" x="570" y="40" width="100" height="25"
        rx="10" />
      <text x="620" y="60">b[7]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[5].pressed }" x="570" y="70" width="100" height="25"
        rx="10" />
      <text x="620" y="90">b[5]</text>
    </g>

    <g data-comment="left four direction buttons">
      <rect class="button" :class="{ pressed: gamepad0?.buttons[12].pressed }" x="157" y="127" width="46" height="46"
        rx="5" />
      <text x="180" y="155">b[12]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[13].pressed }" x="157" y="223" width="46" height="46"
        rx="5" />
      <text x="180" y="250">b[13]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[14].pressed }" x="105" y="175" width="46" height="46"
        rx="5" />
      <text x="128" y="203">b[14]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[15].pressed }" x="208" y="175" width="46" height="46"
        rx="5" />
      <text x="231" y="203">b[15]</text>
    </g>

    <g data-comment="right four direction buttons">
      <rect class="button" :class="{ pressed: gamepad0?.buttons[3].pressed }" x="597" y="127" width="46" height="46"
        rx="5" />
      <text x="620" y="155">b[3]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[0].pressed }" x="597" y="223" width="46" height="46"
        rx="5" />
      <text x="620" y="250">b[0]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[2].pressed }" x="545" y="175" width="46" height="46"
        rx="5" />
      <text x="568" y="203">b[2]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[1].pressed }" x="648" y="175" width="46" height="46"
        rx="5" />
      <text x="671" y="203">b[1]</text>
    </g>

    <g data-comment="center body buttons">
      <circle class="button" :class="{ pressed: gamepad0?.buttons[16].pressed }" r="30" cx="400" cy="150" />
      <text x="400" y="155">b[16]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[8].pressed }" x="330" y="200" width="40" height="20"
        rx="8" />
      <text x="350" y="216">b[8]</text>
      <rect class="button" :class="{ pressed: gamepad0?.buttons[9].pressed }" x="430" y="200" width="40" height="20"
        rx="8" />
      <text x="450" y="216">b[9]</text>
    </g>

    <g data-comment="left wheel">
      <circle class="button" :class="{ pressed: gamepad0?.buttons[10].pressed }" r="38" cx="260" cy="280" />
      <text x="260" y="285">b[10]</text>
      <text class="axis" x="180" y="360">axes[0]</text>
      <text class="axis" x="210" y="380" style="font-size: 15px; text-anchor: end;">Horizontal axis</text>
      <text class="axis" x="260" y="360">axes[1]</text>
      <text class="axis" x="230" y="380" style="font-size: 15px; text-anchor: start;">Vertical axis</text>
    </g>
    <g data-comment="right wheel">
      <circle class="button" :class="{ pressed: gamepad0?.buttons[11].pressed }" r="38" cx="540" cy="280" />
      <text x="540" y="285">b[11]</text>
      <text class="axis" x="540" y="360">axes[3]</text>
      <text class="axis" x="570" y="380" style="font-size: 15px; text-anchor: end;">Vertical axis</text>
      <text class="axis" x="620" y="360">axes[2]</text>
      <text class="axis" x="590" y="380" style="font-size: 15px; text-anchor: start;">Horizontal axis</text>
    </g>

  </svg>

  <h2>Raw View </h2>
  <ul v-if="gamepad0">
    <li><label>Id</label> {{ gamepad0.id }}</li>
    <li><label>Index</label> {{ gamepad0.index }}</li>
    <li><label>Mapping</label> {{ gamepad0.mapping }}</li>
    <li><label>Timestamp</label> {{ gamepad0.timestamp }}</li>
    <li><label>Connected</label> {{ gamepad0.connected }}</li>
    <li>
      <label>Buttons</label>
      <ul>
        <li v-for="(button, index) of gamepad0.buttons">
          <span class="button-column" style=" min-width: 2rem; text-align: right;">{{ index }}</span> |
          <span class="button-column">{{ button.pressed ? 'pressed' : '' }}</span> |
          <span class="button-column">{{ button.value.toFixed(2) }}</span> |
          <span class="button-column">{{ button.touched ? 'touched' : '' }}</span>
        </li>
      </ul>
    </li>
    <li>
      <label>Axes</label>
      <ul>
        <li v-for="(axis, index) of gamepad0.axes">
          <span class="button-column" style=" min-width: 2rem; text-align: right;">{{ index }}</span> |
          <span class="button-column"> {{ axis }} </span>
        </li>
      </ul>
    </li>
    <li v-if="gamepad0.vibrationActuator">
      <label>Vibration Actuator</label>
      <ul>
        <li><label>Type</label> {{ gamepad0.vibrationActuator.type }}</li>
        <li><label>playEffect</label> <button type="button" @click="console.log('nothing')">TODO</button></li>
        <li><label>reset</label> <button type="button" @click="console.log('nothing')">TODO</button>
        </li>
      </ul>
    </li>
  </ul>

  <h2>More info for Xbox Controller </h2>
  <ul>
    <li>b[6] -> LT (left trigger)</li>
    <li>b[4] -> LB (left bumper)</li>
    <li>b[7] -> LT (right trigger)</li>
    <li>b[5] -> LB (right bumper)</li>
    <li>b[2] -> X</li>
    <li>b[3] -> Y</li>
    <li>b[0] -> A</li>
    <li>b[1] -> B</li>
  </ul>
</template>

<style scoped>
svg {
  border: 1px solid black;
}

svg text {
  text-anchor: middle;
  font-size: 20px;
}

svg .body {
  fill: rgb(161, 161, 161);
  stroke: black;
  stroke-width: 2;
}

svg .button {
  fill: white;
  stroke: black;
  stroke-width: 2;
}

svg .button.pressed {
  fill: lightskyblue;
  stroke: teal;
  stroke-width: 4;
}

svg .axis {
  fill: rebeccapurple;
}

ul li label {
  display: inline-block;
  font-weight: bold;
  min-width: 100px;
  margin-right: 10px;
}

ul .button-column {
  display: inline-block;
  min-width: 80px;
  text-align: center;
}
</style>
