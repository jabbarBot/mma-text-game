<template>
  <div class="contracts">
    <h1>Fighter Contracts</h1>
    <div v-if="!squad.length">
      <p>You have no fighters under contract. Scout and sign new talent below!</p>
    </div>
    <div>
      <h2>Your Squad</h2>
      <div v-if="squad.length">
        <ul>
          <li v-for="f in squad" :key="f.id">
            {{ f.name }} ({{ f.statline }})
            <span> | Contract: {{ f.contractYears }}y/{{ f.salary }} coins/yr </span>
            <button @click="releaseFighter(f.id)">Release</button>
            <button @click="negotiateFighter(f.id)">Negotiate</button>
          </li>
        </ul>
      </div>
      <div v-else>
        <p>No fighters in your squad.</p>
      </div>
    </div>
    <div>
      <h2>Available Free Agents</h2>
      <ul>
        <li v-for="fa in freeAgents" :key="fa.id">
          {{ fa.name }} ({{ fa.statline }})
          <button @click="signFighter(fa.id)">Sign ({{ fa.salary }} coins/yr, {{ fa.contractYears }} yrs)</button>
        </li>
      </ul>
    </div>
    <div v-if="negotiatingFighter">
      <h3>Negotiation: {{ negotiatingFighter.name }}</h3>
      <div>
        <label>Offer Salary: <input v-model.number="offerSalary" type="number" min="1" /></label>
        <label>Contract Years: <input v-model.number="offerYears" type="number" min="1" max="5" /></label>
        <button @click="finalizeNegotiation">Propose Deal</button>
        <button @click="cancelNegotiation">Cancel</button>
        <p v-if="negotiationMsg">{{ negotiationMsg }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

function makeFighter(name) {
  const power = Math.floor(Math.random() * 10) + 5
  const defense = Math.floor(Math.random() * 10) + 5
  const luck = Math.floor(Math.random() * 10) + 5
  const contractYears = Math.floor(Math.random() * 5) + 1
  const salary = (power + defense + luck) * (Math.floor(Math.random() * 10) + 5)
  return {
    id: crypto.randomUUID(),
    name,
    power,
    defense,
    luck,
    contractYears,
    salary,
    statline: `Pwr ${power} | Def ${defense} | Luck ${luck}`
  }
}

const starterNames = ["Bonecrusher", "Viper", "Tiger", "Brawler", "Ghost", "Blade", "Juggernaut"]
const squad = ref([
  makeFighter("ACE"),
])
const freeAgents = ref(
  starterNames.map(makeFighter)
)

function signFighter(id) {
  const idx = freeAgents.value.findIndex(f => f.id === id)
  if(idx !== -1) {
    squad.value.push(freeAgents.value[idx])
    freeAgents.value.splice(idx, 1)
  }
}
function releaseFighter(id) {
  const idx = squad.value.findIndex(f => f.id === id)
  if(idx !== -1) {
    freeAgents.value.push(squad.value[idx])
    squad.value.splice(idx, 1)
  }
}

const negotiatingFighter = ref(null)
const offerSalary = ref(0)
const offerYears = ref(1)
const negotiationMsg = ref('')

function negotiateFighter(id) {
  const fighter = squad.value.find(f => f.id === id)
  if(fighter) {
    negotiatingFighter.value = fighter
    offerSalary.value = fighter.salary
    offerYears.value = fighter.contractYears
    negotiationMsg.value = ''
  }
}
function finalizeNegotiation() {
  if(!negotiatingFighter.value) return
  // 70% base chance, +15% if offer is better than current
  let success = Math.random() < (0.7 + ((offerSalary.value > negotiatingFighter.value.salary || offerYears.value > negotiatingFighter.value.contractYears) ? 0.15 : 0))
  if(success) {
    negotiatingFighter.value.salary = offerSalary.value
    negotiatingFighter.value.contractYears = offerYears.value
    negotiationMsg.value = 'Negotiation successful! New contract signed.'
  } else {
    negotiationMsg.value = 'Negotiation failed. Fighter will stick with their old contract.'
  }
}
function cancelNegotiation() {
  negotiatingFighter.value = null
}
</script>

<style scoped>
.contracts {
  background: #161a1e;
  color: #f2f2f2;
  padding: 2em;
  border-radius: 12px;
  max-width: 480px;
  margin: 2em auto;
  font-family: sans-serif;
}
ul {
  padding:0 1em 1em 1.2em;
}
li {
  margin-bottom: 0.7em;
}
button {
  margin-left: 1em;
  padding: 0.2em 0.7em;
  background: #73e255;
  color: #23232c;
  border: none;
  border-radius: 7px;
  font-weight: bold;
  cursor: pointer;
}
button:disabled {
  background: #999;
}
input[type="number"] {
  width: 60px;
  margin-left: 0.6em;
}
</style>
