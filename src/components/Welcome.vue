<template>
  <h1>Basketball Recorder</h1>
  <div class="input_field">
    <div class="input input-group">
        <label for="date" class="input-group-text">Date: </label>
        <input type="date" id="date" ref="date" class="form-control">
    </div>
    <div class="input input-group">
        <label for="time" class="input-group-text">Time: </label>
        <input type="time" id="time" ref="time" class="form-control">
    </div>
    <div class="input input-group">
        <label for="location" class="input-group-text">Location: </label>
        <input type="text" id="location" ref="location" class="form-control">
    </div>
    <div class="input input-group">
        <label for="opponent" class="input-group-text">Opponent: </label>
        <input type="text" id="opponent" ref="opponent" class="form-control">
    </div>
    <button @click="create_game" class="btn">Create</button>
  </div>
</template>

<script>
export default {
    // emits game information when click create button
    emits: ['create_game'], 
    methods: {
        create_game() {
            this.$emit('create_game', {
                date: this.$refs.date.value, 
                time: this.$refs.time.value, 
                location: this.$refs.location.value, 
                opponent: this.$refs.opponent.value, 
                points: 0
            })
        }
    },
}
</script>

<style scoped>
h1 {
    margin-top: 10%;
}

.input_field {
    width: 50%;
    margin-left: auto;
    margin-right: auto;
    margin-top: 10%;
    padding: 2% 5%;
    border: #bcc5cb 1px solid;
    border-radius: 2%;
}

.input {
    margin-top: 5%;
    margin-bottom: 5%;
}

.btn {
  background-color: #d0dae1;
  margin-top: 8%;
}

.btn:hover {
    border: #bcc5cb 1px dashed;
}
</style>