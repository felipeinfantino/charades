<template>
  <div>
    <div v-if="state === STATES[0]">
      <Welcome @nextState="nextState" />
    </div>
    <div v-if="state === STATES[1]">
    <SelectTeamSize  @teamsCreated="teamsCreated"/>
    </div>
    <div v-if="state === STATES[2]">
     <PlayerNamesInput v-bind:playersTeam1="playersTeam1" v-bind:playersTeam2="playersTeam2" @playersCreated="playersCreated" />
    </div>
    <div v-if="state === STATES[3]">
    <CharacterInput v-bind:allPlayers="allPlayers" @charactersCreated="charactersCreated" />
    </div>
    <div v-if="state === STATES[4]">
      <h3 v-if="!startRound">Let the game beginn</h3>
      <h4>Round {{round}}</h4>
      <h6>Global score : {{globalScore.team1}} : {{globalScore.team2}}</h6>
      <h6>Round score : {{roundScore.team1}} : {{roundScore.team2}}</h6>
      <h4>Current team {{currentTeam}}</h4>
      <h4>Current player {{currentTeam === 1 ? playersTeam1[currentPlayerTeam1] : playersTeam2[currentPlayerTeam2]}}</h4>
      <br />
      <br />
      <br />
      <br />
      <button v-if="!startRound" class="btn btn-secondary" @click="start">Start</button>
      <div v-if="startRound">
        <h5>Character to guess</h5>
        <h6>{{currentCharacter}}</h6>
        <button class="btn btn-secondary" @click="nextCharacter">Next</button>
        <circular-count-down-timer
          :initial-value="60"
          :steps="60"
          :size="100"
          :paused="paused"
          @update="updated"
          @finish="timeFinished"
          ref="countdown"
        ></circular-count-down-timer>
      </div>
    </div>
    <div v-if="state === STATES[5]">
      <h3>Game over</h3>
      <h4>The score is</h4>
      <h4>Team 1 : {{globalScore.team1}} , Team 2 : {{globalScore.team2}}</h4>
      <h4 v-if="globalScore.team1 === globalScore.team2">It`s a Tie</h4>
      <h4
        v-if="globalScore.team1 !== globalScore.team2"
      >The winner is {{globalScore.team1 > globalScore.team2 ? "Team 1": "Team 2"}}</h4>
      <button class="btn btn-secondary" @click="playAgainSameTeams">Play again with same teams</button>
      <button
        class="btn btn-secondary"
        @click="playAgainShuffledTeams"
      >Play again with shuffled teams</button>
      <button
        class="btn btn-secondary"
        @click="playAgainDifferentTeams"
      >Play again with different players</button>
    </div>
    <div v-if="state === STATES[6]">
      <h3>New teams</h3>
      <div class="container">
        <div class="row">
          <div class="col">
            <div class="row" v-for="(player, index) in playersTeam1" v-bind:key="index">
              <h6>{{playersTeam1[index]}}</h6>
            </div>
          </div>
          <div class="col">
            <div class="row" v-for="(player, index) in playersTeam2" v-bind:key="index">
              <h6>{{playersTeam2[index]}}</h6>
            </div>
          </div>
        </div>
      </div>
      <button class="btn btn-secondary" @click="state = 'characterInput'">Start</button>
    </div>
  </div>
</template>

<script>
import Welcome from "./Welcome.vue";
import SelectTeamSize from "./SelectTeamSize.vue";
import PlayerNamesInput from "./PlayerNamesInput.vue";
import CharacterInput from "./CharacterInput.vue";

window.onbeforeunload = function() {
  return "";
}

export default {
  name: "Home",
  components: { Welcome, SelectTeamSize,PlayerNamesInput, CharacterInput },
  data() {
    return {
      startRound: false,
      paused: true,
      currentTeam: 1,
      roundScore: { team1: 0, team2: 0 },
      globalScore: { team1: 0, team2: 0 },
      currentPlayerTeam1: 0,
      currentPlayerTeam2: 0,
      currentCharacter: "",
      playersTeam1: [],
      playersTeam2: [],
      allPlayers: [],
      allCharacters: [],
      allCharactersCopy: [],
      round: 1,
      seconds: 0,
      state: "welcome",
      STATES: [
        "welcome",
        "teamSizeSelect",
        "playerNames",
        "characterInput",
        "game",
        "gameOver",
        "shuffledTeams"
      ]
    };
  },
  mounted() {},
  methods: {
    nextState() {
      const index = this.STATES.indexOf(this.state);
      this.state = this.STATES[index + 1];
    },
    teamsCreated(teamsArray) {
      this.playersTeam1 = teamsArray[0];
      this.playersTeam2 = teamsArray[1];
      this.nextState();
    },
    playersCreated(playersObj){
        const {playersTeam1, playersTeam2} = playersObj;
        this.playersTeam1 = playersTeam1;
        this.playersTeam2 = playersTeam2;
        this.allPlayers = this.playersTeam1.concat(this.playersTeam2);
        console.log(this.playersTeam1)
        console.log(this.playersTeam2)
        console.log(this.allPlayers)
        this.nextState();
    },
    timeFinished() {
      this.beep();
      this.updatePlayer();
      this.resetCountdown();
      this.pauseClock();
      this.changeTeam();
      this.allCharactersCopy.push(this.currentCharacter);
      this.startRound = false;
    },
    charactersCreated(allCharacters) {
        this.allCharacters = allCharacters
        this.allCharactersCopy = [...this.allCharacters]
        this.shuffle(this.allCharactersCopy);
        this.nextState();
    },
    start() {
      this.startRound = true;
      this.getCharacterToGuess();
      this.startClock();
    },
    resetRound() {
      this.startRound = false;
    },
    startClock() {
      this.paused = false;
    },
    pauseClock() {
      this.paused = true;
    },
    beep() {
      var audio = new Audio(require("./../assets/beep.wav")); // path to file
      audio.play();
    },
    resetCountdown() {
      const secondsToAdd = 60 - this.seconds;
      this.$refs.countdown.updateTime(secondsToAdd);
    },
    updated(status) {
      this.seconds = status["value"]; //{"value": 144, "seconds": 24, "minutes": 2, "hours": 0}
    },
    nextCharacter() {
      this.addPoint();
      if (this.allCharactersCopy.length === 0) {
        this.finishRound();
      } else {
        this.getCharacterToGuess();
      }
    },
    finishRound() {
      // round is done
      this.changeTeam();
      this.resetRoundPoints();
      this.pauseClock();
      this.resetCountdown();
      this.updatePlayer();
      this.resetCharacters();
      this.resetRound();
      this.round++;
      if (this.round > 3) {
        this.nextState();
      }
    },
    getCharacterToGuess() {
      this.currentCharacter = this.allCharactersCopy.pop(); // already shuffled
    },
    resetCharacters() {
      this.allCharactersCopy = [...this.allCharacters];
      this.shuffle(this.allCharactersCopy);
    },
    updatePlayer() {
      if (this.currentTeam === 1) {
        if (this.currentPlayerTeam1 === this.playersTeam1.length - 1) {
          this.currentPlayerTeam1 = 0;
        } else {
          this.currentPlayerTeam1++;
        }
      } else {
        if (this.currentPlayerTeam2 === this.playersTeam2.length - 1) {
          this.currentPlayerTeam2 = 0;
        } else {
          this.currentPlayerTeam2++;
        }
      }
    },
    shuffle(array) {
      array.sort(() => Math.random() - 0.5);
    },
    addPoint() {
      if (this.currentTeam === 1) {
        this.globalScore["team1"]++;
        this.roundScore["team1"]++;
      } else {
        this.globalScore["team2"]++;
        this.roundScore["team2"]++;
      }
    },
    resetRoundPoints() {
      this.roundScore = { team1: 0, team2: 0 };
    },
    changeTeam() {
      if (this.currentTeam === 1) {
        this.currentTeam = 2;
      } else {
        this.currentTeam = 1;
      }
    },
    playAgainSameTeams() {
      this.resetGame();
      this.state = "characterInput";
    },
    playAgainShuffledTeams() {
      this.resetGame();
      this.shuffleTeams();
      this.nextState();
      this.currentPlayerTeam1 = 0;
      this.currentPlayerTeam2 = 0;
    },
    shuffleTeams() {
      const oldTeam1 = [...this.playersTeam1];
      oldTeam1.sort();
      const oldTeam2 = [...this.playersTeam2];
      oldTeam2.sort();

      const copy = [...this.allPlayers];
      this.shuffle(copy);

      let newTeam1 = copy.slice(0, copy.length / 2);
      newTeam1.sort();

      while (
        JSON.stringify(newTeam1) == JSON.stringify(oldTeam1) ||
        JSON.stringify(newTeam1) == JSON.stringify(oldTeam2)
      ) {
        this.shuffle(copy);
        newTeam1 = copy.slice(0, copy.length / 2);
        newTeam1.sort();
      }
      this.allPlayers = copy;
      // we dont want to change allPlayers
      const copy2 = [...copy];
      this.playersTeam1 = copy2.splice(0, copy2.length / 2);
      this.playersTeam2 = copy2;
      console.log("team1 ", this.playersTeam1);
      console.log("team2 ", this.playersTeam2);
    },
    playAgainDifferentTeams() {
      this.resetGame();
      this.playersTeam1 = [];
      this.playersTeam2 = [];
      this.allPlayers = [];
      this.state = "teamSizeSelect";
      this.currentPlayerTeam1 = 0;
      this.currentPlayerTeam2 = 0;
    },
    resetGlobalScore() {
      this.globalScore = { team1: 0, team2: 0 };
    },
    resetGame() {
      this.resetRoundPoints();
      this.resetGlobalScore();
      this.resetRound();
      this.allCharacters = [];
      this.allCharactersCopy = [];
      this.currentPlayerCharacterInput = 0;
      this.round = 1;
      this.currentCharacter = "";
    }
  }
};
</script>