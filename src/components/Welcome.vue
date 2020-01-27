<template>
  <div>
      Welcome to charades
      <div class="container">
        <div class="row">
          <div class="col">
            <button class="btn btn-secondary" v-on:click="nextState">Start game</button>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
export default {
  name: 'Welcome',
  methods: {
      nextState(){
          this.$emit("nextState")
      }
  }
}
</script>