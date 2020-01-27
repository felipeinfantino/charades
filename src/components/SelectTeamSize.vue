<template>
  <div>
    <h2>Number of players per team</h2>
    <br />
    <h5 v-if="team_size === 0">Please select a team size</h5>
    <h5 v-if="team_size > 0">Selected team size : {{team_size}}</h5>
    <b-dropdown text="Team size">
      <b-dropdown-item @click="selectTeamSize(2)">2</b-dropdown-item>
      <b-dropdown-item @click="selectTeamSize(3)">3</b-dropdown-item>
      <b-dropdown-item @click="selectTeamSize(4)">4</b-dropdown-item>
      <b-dropdown-item @click="selectTeamSize(5)">5</b-dropdown-item>
    </b-dropdown>
    <br />
    <br />
    <button v-if="team_size > 0" class="btn btn-secondary" @click="createTeams">Next</button>
  </div>
</template>

<script>
export default {
  name: "SelectTeamSize",
  data() {
    return {
      team_size: 0
    };
  },
  methods: {
    selectTeamSize(size) {
      this.team_size = size;
    },
    createTeams() {
      const playersArrayTeam1 = new Array(this.team_size).fill("");
      const playersArrayTeam2 = new Array(this.team_size).fill("");
      this.$emit("teamsCreated", [playersArrayTeam1, playersArrayTeam2]);
    }
  }
};
</script>