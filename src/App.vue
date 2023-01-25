<template>
  <Welcome v-if="!created" @create_game="create"/>
  <Message v-if="created" @delete="prop_delete" :records="records"/>
  <Record_btns v-if="created" @add_record="add_record" :game_info="game_info" :delete_records="delete_records"/>
</template>

<script>
import Welcome from './components/Welcome.vue'
import Record_btns from './components/Record_btns.vue'
import Message from './components/Message.vue'

export default {
  name: 'App',
  components: {
    Welcome, Record_btns, Message
  }, 
  data() {
    return {
      // bool flag for control
      created: false, 
      // information of this game
      game_info: null, 
      records: [], 
      delete_records: {}, 
    }
  },
  methods: {
    create(data) {
      this.created = true
      this.game_info = data
    }, 
    add_record(record) {
      record['index'] = this.records.length
      record['selected'] = false
      this.records.push(record)
    }, 
    prop_delete(records) {
      this.delete_records = records
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 40px;
}
</style>
