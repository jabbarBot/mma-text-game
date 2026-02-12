<template>
  <div class="main-game">
    <h1>MMA Text Game</h1>

    <div v-if="!fighter">
      <h2>Create Your Fighter</h2>
      <input v-model="fighterName" placeholder="Fighter Name" />
      <button @click="createFighter">Create</button>
    </div>

    <div v-else>
      <h2>Welcome, {{ fighter.name }}</h2>
      <div>Stats: 🥊 Power {{ fighter.power }} | 🛡️ Defense {{ fighter.defense }} | 💥 Luck {{ fighter.luck }}</div>
      <div>💰 Coins: {{ currency }} | 🎁 Lootboxes: {{ lootboxes }}</div>

      <div class="fight-section">
        <h3>Fight!</h3>
        <div>Opponent: <b>{{ opponent.name }}</b> (Power: {{ opponent.power }}, Defense: {{ opponent.defense }}, Luck: {{ opponent.luck }})</div>
        <input type="number" v-model.number="bet" placeholder="Bet coins" min="1" :max="currency" />
        <button @click="fight" :disabled="bet < 1 || bet > currency">Fight & Gamble</button>
      </div>

      <div v-if="fightResult" class="result-section">
        <div v-html="fightResult"></div>
        <button v-if="lootboxes > 0" @click="openLootbox">Open Lootbox</button>
        <div v-if="lootboxMessage">{{ lootboxMessage }}</div>
        <button @click="nextFight">Next Opponent</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const fighter = ref(null)
const fighterName = ref('')
const currency = ref(100)
const lootboxes = ref(0)
const lootboxMessage = ref('')
const fightResult = ref('')
const bet = ref(10)
const opponent = ref(randomOpponent())

function createFighter() {
  fighter.value = {
    name: fighterName.value,
    power: randStat(),
    defense: randStat(),
    luck: randStat(),
  }
}

function randStat() {
  return Math.floor(Math.random() * 10) + 5
}

function randomOpponent() {
  const names = ['Crusher', 'Viper', 'Iron Fist', 'Slammer', 'Raging Bull']
  return {
    name: names[Math.floor(Math.random() * names.length)],
    power: randStat(),
    defense: randStat(),
    luck: randStat(),
  }
}

function fight() {
  lootboxMessage.value = ''
  // Add some basic fight logic and gambling
  let playerScore =
    fighter.value.power * Math.random() +
    fighter.value.defense * Math.random() +
    fighter.value.luck * Math.random()
  let opponentScore =
    opponent.value.power * Math.random() +
    opponent.value.defense * Math.random() +
    opponent.value.luck * Math.random()
  let outcomeMsg = ''
  if (playerScore > opponentScore) {
    currency.value += bet.value
    lootboxes.value += 1
    outcomeMsg = `<b>Victory!</b> You won ${bet.value} coins and a lootbox!`
  } else {
    currency.value -= bet.value
    outcomeMsg = `<b>Defeat!</b> You lost ${bet.value} coins.`
  }
  fightResult.value = outcomeMsg
}

function openLootbox() {
  if (lootboxes.value > 0) {
    lootboxes.value -= 1
    // Simulate lootbox reward
    const rewards = [
      { type: 'coins', amount: randLoot(10, 100) },
      { type: 'power', amount: 1 },
      { type: 'defense', amount: 1 },
      { type: 'luck', amount: 1 },
    ]
    const reward = rewards[Math.floor(Math.random() * rewards.length)]
    if (reward.type === 'coins') {
      currency.value += reward.amount
      lootboxMessage.value = `🎉 You won ${reward.amount} coins from the lootbox!`
    } else {
      fighter.value[reward.type] += reward.amount
      lootboxMessage.value = `💪 Your ${reward.type} increased by ${reward.amount} from the lootbox!`
    }
  }
}

function randLoot(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min
}

function nextFight() {
  fightResult.value = ''
  opponent.value = randomOpponent()
}
</script>

<style scoped>
.main-game {
  max-width: 420px;
  margin: auto;
  background: #14151a;
  color: white;
  padding: 2em;
  border-radius: 15px;
  box-shadow: 0 0 12px #090c1499;
}
input {
  margin: 0.5em 0.2em;
  padding: 0.5em;
  border-radius: 7px;
  border: none;
}
button {
  margin: 0.5em;
  padding: 0.5em 1em;
  background: #3ce250;
  color: #1b1919;
  border: none;
  border-radius: 7px;
  font-weight: bold;
  cursor: pointer;
  transition: background 0.2s;
}
button:disabled {
  background: #aaa;
  color: #444;
  cursor: not-allowed;
}
.result-section {
  margin-top: 1em;
  padding: 1em;
  background: #23243a;
  border-radius: 10px;
}
</style>
