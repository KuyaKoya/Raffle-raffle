<template>
  <div id="app">
    <h1>Raffle System</h1>

    <!-- Participant input -->
    <div>
      <label for="participantName">Participant Name:</label>
      <input v-model="newParticipant" type="text" id="participantName" />
      <button @click="addParticipant">Add Participant</button>
    </div>

    <!-- Prize input -->
    <div>
      <label for="prizeName">Prize Name:</label>
      <input v-model="newPrize.name" type="text" id="prizeName" />
      <label for="prizeType">Prize Type:</label>
      <select v-model="newPrize.type" id="prizeType">
        <option value="major">Major</option>
        <option value="minor">Minor</option>
      </select>
      <button @click="addPrize">Add Prize</button>
    </div>

    <!-- Configuration -->
    <div>
      <label for="randomness">Randomness (1-100):</label>
      <input
        v-model.number="randomness"
        type="number"
        id="randomness"
        min="1"
        max="100"
      />
    </div>

    <!-- Run Raffle -->
    <div>
      <button @click="runRaffle">Run Raffle</button>
    </div>

    <!-- Display Winner -->
    <div v-if="winner">
      <h2>Winner: {{ winner.name }}</h2>
      <h3>Prize: {{ winner.name }} ({{ winner.prizeWon }})</h3>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

const participants = ref<string[]>([]);
const newParticipant = ref("");
const prizes = ref<{ name: string; type: string }[]>([]);
const newPrize = ref({ name: "", type: "major" });
const winner = ref<{ name: string; prizeWon: string }>({
  name: "",
  prizeWon: "",
});
const randomness = ref(50);

function addParticipant() {
  if (newParticipant.value.trim() !== "") {
    participants.value.push(newParticipant.value);
    newParticipant.value = "";
  }
}
function addPrize() {
  if (newPrize.value.name.trim() !== "") {
    prizes.value.push({ name: newPrize.value.name, type: newPrize.value.type });
    newPrize.value = { name: "", type: "major" };
  }
}
function runRaffle() {
  if (participants.value.length === 0 || prizes.value.length === 0) {
    alert("Add participants and prizes before running the raffle.");
    return;
  }

  const randomValue = Math.random() * 100;
  const isMajor = randomValue <= randomness.value;

  const eligiblePrizes = prizes.value.filter(
    (prize) => prize.type === (isMajor ? "major" : "minor")
  );

  if (eligiblePrizes.length === 0) {
    alert("No eligible prizes for the selected randomness.");
    return;
  }

  const randomParticipantIndex = Math.floor(
    Math.random() * participants.value.length
  );
  const randomPrizeIndex = Math.floor(Math.random() * eligiblePrizes.length);

  const tempWinner = participants.value[randomParticipantIndex];
  const prizeWon = eligiblePrizes[randomPrizeIndex];

  // Remove winner and prize from the list
  participants.value.splice(randomParticipantIndex, 1);
  prizes.value = prizes.value.filter((prize) => prize !== prizeWon);

  // Display winner
  winner.value = { name: tempWinner, prizeWon: prizeWon.name };

  // You can add animations here if needed
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
