<template>
  <div>
    <h2>Team names</h2>
    <div class="container">
      <div class="row">
        <div class="col">
          Team 1
          <div class="row" v-for="(player, index) in playersTeam1" v-bind:key="index">
            <input v-model="playersTeam1[index]" placeholder="edit me" />
          </div>
        </div>
        <div class="col">
          Team 2
          <div class="row" v-for="(player, index) in playersTeam2" v-bind:key="index">
            <input v-model="playersTeam2[index]" placeholder="edit me" />
          </div>
        </div>
      </div>
      <br />
      <button @click="validateTeams" class="btn btn-secondary">Next</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "PlayerNamesInput",
  props: ["playersTeam1", "playersTeam2"],
  methods: {
    validateTeams() {
      if (
        this.playersTeam1.indexOf("") === -1 &&
        this.playersTeam2.indexOf("") === -1
      ) {
        this.$emit("playersCreated", {playersTeam1: [...this.playersTeam1], playersTeam2: [...this.playersTeam2]});
      } else {
        alert("Please fill all the team members");
      }
    }
  }
};
</script>