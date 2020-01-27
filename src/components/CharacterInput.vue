<template>
  <div>
    <h3>Select the characters</h3>
    <h4>{{allPlayers[playerIndex]}}`s turn to choose 3 famous characters</h4>
    <br />
    <div class="container">
      <div class="col">
        <div class="row">
          <input v-model="character1" placeholder="Famous character 1" style="margin: 0 auto" />
        </div>
        <div class="row">
          <input v-model="character2" placeholder="Famous character 2" style="margin: 0 auto" />
        </div>
        <div class="row">
          <input v-model="character3" placeholder="Famous character 3" style="margin: 0 auto" />
        </div>
      </div>
    </div>
    <br />

    <button class="btn btn-secondary" @click="addCharacters">Next</button>
  </div>
</template>

<script>
export default {
  name: "CharacterInput",
  props: ["allPlayers"],
  data() {
    return {
      playerIndex: 0,
      character1: "",
      character2: "",
      character3: "",
      allCharacters: []
    };
  },
  methods: {
    addCharacters() {
      //check if repeated characters
      const set = new Set([this.character1, this.character2, this.character3]);
      if (set.size < 3) {
        alert("all the characters must be different");
        return;
      }
      if (
        this.character1 === "" ||
        this.character2 === "" ||
        this.character3 === ""
      ) {
        alert("please fill all the characters");
        return;
      }
      if (this.allCharacters.includes(this.character1)) {
        alert(
          "Character 1 was already selected by someone else, please change it"
        );
        return;
      }
      if (this.allCharacters.includes(this.character2)) {
        alert(
          "Character 2 was already selected by someone else, please change it"
        );
        return;
      }
      if (this.allCharacters.includes(this.character3)) {
        alert(
          "Character 3 was already selected by someone else, please change it"
        );
        return;
      }
      this.allCharacters.push(
        this.character1,
        this.character2,
        this.character3
      );
      this.character1 = "";
      this.character2 = "";
      this.character3 = "";
      this.playerIndex++;
      if (this.playerIndex === this.allPlayers.length) {
        this.$emit("charactersCreated", [...this.allCharacters])
      }
    }
  }
};
</script>