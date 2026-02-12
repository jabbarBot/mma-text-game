<template>
  <div class="training">
    <h1>Fighter Training System</h1>
    <div>
      <h2>Your Squad</h2>
      <ul>
        <li v-for="f in squad" :key="f.id">
          <strong>{{ f.name }}</strong>: Power {{ f.power }}, Defense {{ f.defense }}, Luck {{ f.luck }}, Stamina {{ f.stamina }}
          <span v-if="f.cooldown"> — recovering, {{ f.cooldown }} turns left</span>
          <div v-else>
            <label>
              <select v-model="f.selectedArea">
                <option value="power">Power</option>
                <option value="defense">Defense</option>
                <option value="luck">Luck</option>
                <option value="stamina">Stamina</option>
              </select>
            </label>
            <button @click="trainFighter(f)">Train</button>
          </div>
        </li>
      </ul>
    </div>
    <div v-if="trainingLog.length">
      <h2>Training Log</h2>
      <ul>
        <li v-for="(msg, idx) in trainingLog" :key="idx">{{ msg }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue"

function makeFighter(name) {
  return {
    id: crypto.randomUUID(),
    name,
    power: Math.floor(Math.random() * 10) + 5,
    defense: Math.floor(Math.random() * 10) + 5,
    luck: Math.floor(Math.random() * 10) + 5,
    stamina: Math.floor(Math.random() * 10) + 5,
    selectedArea: "power",
    cooldown: 0,
  }
}

const squad = ref([
  makeFighter("ACE"),
  makeFighter("Juggernaut"),
  makeFighter("Blade"),
])

const trainingLog = ref([])

function trainFighter(fighter) {
  if (fighter.cooldown && fighter.cooldown > 0) return
  const area = fighter.selectedArea
  // Training result (60% success)
  const success = Math.random() < 0.6
  if (success) {
    fighter[area]++
    trainingLog.value.unshift(`✅ ${fighter.name} improved their ${area} to ${fighter[area]}!`)
  } else {
    trainingLog.value.unshift(`⚠️ ${fighter.name}'s training for ${area} failed. No gain.`)
  }
  // Add cooldown: 3 turns before next training
  fighter.cooldown = 3
}

// Simulate cooldown decrements (call after each 'turn')
function nextTurn() {
  squad.value.forEach(f => {
    if (f.cooldown && f.cooldown > 0) f.cooldown--
  })
}

</script>

<style scoped>
.training {
  background: #1c2127;
  color: #fafbf6;
  min-width: 400px;
  max-width: 500px;
  margin: 2em auto;
  border-radius: 15px;
  padding: 2em;
  font-family: sans-serif;
}
ul { padding-left: 1.3em; }
button {
  margin-left: 0.8em;
  border: none;
  background: #44d65a;
  color: #19201e;
  border-radius: 7px;
  font-weight: bold;
  padding: 0.4em 1em;
  cursor: pointer;
}
button:disabled { background: #aaa; color: #636363; }
label { margin-left: 0.7em; }
</style>
