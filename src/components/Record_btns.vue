<template>
  <!-- panel: include all the buttons and showed data -->
  <div class="col" style="margin: 4%;">
    <!-- players_panel: handle select for each chosen player -->
    <div class="players_panel row">
      <span v-if="sorted_current_players.length == 0" style="display: inline-flex; width: 400px; align-items: center; justify-content: center;">
        Please select five starting players !!
      </span>
      <template v-for="(player, index) in sorted_current_players" :key="player">
        <span :id="index" @click="choose_player" :class="{'dot': !clicked_player[index], 'clicked_dot': clicked_player[index]}">{{ player }}</span>
      </template>
      <!-- button for changing players -->
      <div class="empty">
        <!-- players list for changing players (not done) -->
        <button type="button" class="btn" @click="temp_players = [...current_players]" data-bs-toggle="modal" data-bs-target="#select">Select</button>
        <div class="modal fade" id="select" tabindex="-1" aria-labelledby="selectLabel" aria-hidden="true">
          <div class="modal-dialog modal-lg">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title">選擇上場球員</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
              </div>
              <div class="modal-body">
                <span v-if="sorted_temp_players.length == 0" style="display: inline-flex; margin: 3%; align-items: center; justify-content: center;">
                  Please select five players !!
                </span>
                <template v-for="(player, index) in sorted_temp_players" :key="player">
                  <span :id="index" class="small_dot" @click="remove_player(player)">{{ player }}</span>
                  <button class="btn-close switch_close" @click="remove_player(player)"></button>
                </template>
                <div class="modal-footer">
                  <span v-if="current_players.length > 0" style="position: absolute; left: 7%; top: 16%">最近上場</span>
                  <button type="button" class="btn" data-bs-dismiss="modal">Cancel</button>
                  <button @click="save_players_list" :disabled="temp_players.length != 5" type="button" class="btn" data-bs-dismiss="modal">Save</button>
                </div>
                <div v-if="current_players.length > 0" id="recently">
                  <template v-for="(player, id) in get_on_players_info" :key="id">
                    <span :id="'p' + player.id" :class="{'xs_dot': !player.on_board, 'colored_xs_dot': player.on_board}" @click="add_player(player.id)">{{ player.id }}</span>
                  </template>
                  <div class="small_hr"></div>
                </div>
                <template v-for="(player, id) in get_not_on_players_info" :key="id">
                  <span :id="'p' + player.id" :class="{'xs_dot': !player.on_board, 'colored_xs_dot': (player.on_board || player_in_temp_players(player.id))}" @click="add_player(player.id)">{{ player.id }}</span>
                </template>
              </div>
              
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="hr row"></div>
    <!-- record_panel: panel for adding record for each selected player -->
    <div class="record_panel row">
      <!-- type_panel: select record type -->
      <div class="type_panel col">
        <div class="type_panel_line1 row">
          <span @click="choose_record" id="assist" :class="{'record_dot': !clicked_record['assist'], 'clicked_record_dot': clicked_record['assist']}">助攻</span>
          <span @click="choose_record" id="steal" :class="{'record_dot': !clicked_record['steal'], 'clicked_record_dot': clicked_record['steal']}">抄截</span>
          <span @click="choose_record" id="block" :class="{'record_dot': !clicked_record['block'], 'clicked_record_dot': clicked_record['block']}">阻攻</span>
          <span @click="choose_record" id="foul" :class="{'record_dot': !clicked_record['foul'], 'clicked_record_dot': clicked_record['foul']}">犯規</span>
          <span @click="choose_record" id="turnover" :class="{'record_dot': !clicked_record['turnover'], 'clicked_record_dot': clicked_record['turnover']}">失誤</span>
          <div class="display">
            <span style="font-size: 5vw; font-weight: bold; width: 75px;">{{ game_info.points }}</span>
            <span style="font-size: 0.8vw;">points</span>
          </div>
        </div>
        <div class="type_panel_line2 row">
          <span @click="choose_record" id="point_2" :class="{'record_dot': !clicked_record['point_2'], 'clicked_record_dot': clicked_record['point_2']}">2分進</span>
          <span @click="choose_record" id="point_3" :class="{'record_dot': !clicked_record['point_3'], 'clicked_record_dot': clicked_record['point_3']}">3分進</span>
          <span @click="choose_record" id="point_1" :class="{'record_dot': !clicked_record['point_1'], 'clicked_record_dot': clicked_record['point_1']}">罰球進</span>
          <span @click="choose_record" id="o_rebound" :class="{'record_dot': !clicked_record['o_rebound'], 'clicked_record_dot': clicked_record['o_rebound']}">進攻<br/>籃板</span>
          <div class="wide_empty">
            <div style="width: 100%; font-size: 1.25vw;">
              <label for="period">Period Scores</label>
              <input type="text" id="period" ref="period" class="form-control" v-model="period_score">
              <span style="font-size: 1vw; display: flex; justify-content: flex-end;">* eg. 10:15, 20:25, 30:35, 40:45</span>
            </div>
          </div>
        </div>
        <div class="type_panel_line3 row">
          <span @click="choose_record" id="point_2_miss" :class="{'record_dot': !clicked_record['point_2_miss'], 'clicked_record_dot': clicked_record['point_2_miss']}">2分<br/>不進</span>
          <span @click="choose_record" id="point_3_miss" :class="{'record_dot': !clicked_record['point_3_miss'], 'clicked_record_dot': clicked_record['point_3_miss']}">3分<br/>不進</span>
          <span @click="choose_record" id="point_1_miss" :class="{'record_dot': !clicked_record['point_1_miss'], 'clicked_record_dot': clicked_record['point_1_miss']}">罰球<br/>不進</span>
          <span @click="choose_record" id="d_rebound" :class="{'record_dot': !clicked_record['d_rebound'], 'clicked_record_dot': clicked_record['d_rebound']}">防守<br/>籃板</span>
          <span class="record_dot" @click="confirm" id="confirm">確定</span>
          <div class="empty">
            <!-- Button show and its table modal -->
            <button type="button" class="btn" data-bs-toggle="modal" data-bs-target="#show">Show</button>
            <div class="modal fade" id="show" tabindex="-1" aria-labelledby="exitLabel" aria-hidden="true">
              <div class="modal-dialog modal-lg">
                <div class="modal-content">
                  <div class="modal-header">
                    <h6 class="modal-title" style="margin-right: 3vw; font-size: 1.25vw">日期：{{ game_info.date.slice(5, 10) }}</h6>
                    <h6 class="modal-title" style="margin-right: 3vw; font-size: 1.25vw">時間：{{ game_info.time }}</h6>
                    <h6 class="modal-title" style="margin-right: 3vw; font-size: 1.25vw">地點：{{ game_info.location }}</h6>
                    <h6 class="modal-title" style="margin-right: 3vw; font-size: 1.25vw">對手：{{ game_info.opponent }}</h6>
                    <h6 class="modal-title" style="font-size: 1.25vw">比分：{{ period_score }}</h6>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                  </div>
                  <div class="modal-body">
                    <table class="table" style="font-size: 14px">
                      <thead>
                        <tr>
                          <th scope="col">#</th>
                          <th scope="col">2分進</th>
                          <th scope="col">2分不進</th>
                          <th scope="col">3分進</th>
                          <th scope="col">3分不進</th>
                          <th scope="col">罰球進</th>
                          <th scope="col">罰球不進</th>
                          <th scope="col">攻籃</th>
                          <th scope="col">防籃</th>
                          <th scope="col">助攻</th>
                          <th scope="col">抄截</th>
                          <th scope="col">阻攻</th>
                          <th scope="col">犯規</th>
                          <th scope="col">失誤</th>
                          <th scope="col">總分</th>
                        </tr>
                      </thead>
                      <tbody>
                        <template v-for="(player, id) in players_info" :key="id">
                          <tr v-if="player.on_board">
                            <th scope="row">{{ id }}</th>
                            <td>{{ player.point_2 }}</td>
                            <td>{{ player.point_2_miss }}</td>
                            <td>{{ player.point_3 }}</td>
                            <td>{{ player.point_3_miss }}</td>
                            <td>{{ player.point_1 }}</td>
                            <td>{{ player.point_1_miss }}</td>
                            <td>{{ player.o_rebound }}</td>
                            <td>{{ player.d_rebound }}</td>
                            <td>{{ player.assist }}</td>
                            <td>{{ player.steal }}</td>
                            <td>{{ player.block }}</td>
                            <td>{{ player.foul }}</td>
                            <td>{{ player.turnover }}</td>
                            <td>{{ player.total_points }}</td>
                          </tr>
                        </template>
                      </tbody>
                    </table>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  emits: ['add_record'], 
  props: {
    game_info: {
      type: Object,
      required: true 
    }, 
    delete_records: {
      type: Object
    }
  },
  watch: {
    delete_records(records, _) {
      for (var i = 0; i < records.length; i++) {
        var player = records[i].id, type = records[i].type
        this.players_info[player][type]--;
        if (type == "point_2") {
          this.players_info[player]['total_points'] -= 2
          this.game_info.points -= 2
        } else if (type == "point_3") {
          this.players_info[player]['total_points'] -= 3
          this.game_info.points -= 3
        } else if (type == "point_1") {
          this.players_info[player]['total_points']--
          this.game_info.points--
        }
      }
    }
  },
  data() {
    return {
      clicked_player: [false, false, false, false, false],
      clicked_record: {
        assist: false, steal: false, block: false, foul: false, turnover: false, 
        point_2: false, point_3: false, point_1: false, o_rebound: false,
        point_2_miss: false, point_3_miss: false, point_1_miss: false, d_rebound: false
      },
      current_players: [], 
      temp_players: [], 
      players_info: [], 
      period_score: null, 
    }
  },
  methods: {
    choose_player(event) {
      for (var i = 0; i < 5; i++) {
        if (i == event.target.id) 
          this.clicked_player[i] = !this.clicked_player[i]
        else 
          this.clicked_player[i] = false;
      }
    }, 
    choose_record(event) {
      for (var record in this.clicked_record) {
        if (record == event.target.id) 
          this.clicked_record[record] = !this.clicked_record[record]
        else
        this.clicked_record[record] = false
      }
    }, 
    confirm() {
      var selected_player = this.get_selected_player()
      var selected_record = this.get_selected_record()
      if (selected_player != null && selected_record != null) {
        this.players_info[selected_player][selected_record]++
        this.$emit('add_record', {id: selected_player, type: selected_record})
        if (selected_record == "point_2") {
          this.players_info[selected_player]['total_points'] += 2
          this.game_info.points += 2
        } else if (selected_record == "point_3") {
          this.players_info[selected_player]['total_points'] += 3
          this.game_info.points += 3
        } else if (selected_record == "point_1") {
          this.players_info[selected_player]['total_points']++
          this.game_info.points++
        }
      }
    }, 
    get_selected_player() {
      for (var selected_player = 0; selected_player < 5; selected_player++) {
        if (this.clicked_player[selected_player]) {
          this.clicked_player[selected_player] = false;
          selected_player = this.current_players[selected_player]
          return selected_player;
        }
      }
    },
    get_selected_record() {
      for (var selected_record in this.clicked_record) {
        if (this.clicked_record[selected_record]) {
          this.clicked_record[selected_record] = false;
          return selected_record
        }
      }
    },
    remove_player(player) {
      for ( var i = 0; i < this.temp_players.length; i++) { 
        if (this.temp_players[i] === player) { 
          this.temp_players.splice(i, 1);
        }
      }
    }, 
    add_player(player) {
      if (this.temp_players.length < 5) {
        var f = true
        for (var i = 0; i < this.temp_players.length; i++) {
          if (this.temp_players[i] === player) {
            f = false
            break
          }
        }
        if (f) this.temp_players.push(player)
      }
    }, 
    save_players_list() {
      this.current_players = [...this.temp_players]
      for (var i = 0; i < this.current_players.length; i++) {
        this.players_info[this.current_players[i]].on_board = true;
      }
    }, 
    delete_prop_record(record) {
      console.log(record)
    }, 
    player_in_temp_players(player) {
      for (var i = 0; i < this.temp_players.length; i++) 
        if (this.temp_players[i] == player) 
          return true
      return false
    }
  },
  mounted () {
    // initialize players_info when mounted
    for (var i = 0; i <= 100; i++) {
      this.players_info[i] = {
        assist: 0, steal: 0, block: 0, foul: 0, turnover: 0, total_points: 0, 
        point_2: 0, point_3: 0, point_1: 0, o_rebound: 0, on_board: false, 
        point_2_miss: 0, point_3_miss: 0, point_1_miss: 0, d_rebound: 0, id: i
      }
    }
  },
  computed: {
    sorted_current_players() {
      return this.current_players.sort((a, b) => a - b)
    }, 
    sorted_temp_players() {
      return this.temp_players.sort((a, b) => a - b)
    }, 
    get_on_players_info() {
      var on_players_info = []
      for (var i = 0; i < this.players_info.length; i++) 
        if (this.players_info[i].on_board) 
          on_players_info.push(this.players_info[i])
      return on_players_info
    },
    get_not_on_players_info() {
      var not_on_players_info = []
      for (var i = 0; i < this.players_info.length; i++) 
        if (!this.players_info[i].on_board) 
          not_on_players_info.push(this.players_info[i])
      return not_on_players_info
    }
  },
}
</script>

<style scoped>
#period {
  height: 3.5vw;
}

.switch_close {
  position: relative;
  background-color: #d0dae1;
  border-radius: 50%;
  right: 2vw;
  bottom: 2.25vw;
}

.players_panel {
  margin-left: 2%;
  display: flex;
  justify-content: center;
}

.type_panel_line1 {
  margin-left: 2%;
  display: flex;
  align-items: flex-start;
  justify-content: center;
}

.type_panel_line2 {
  margin-left: 2%;
  display: flex;
  align-items: flex-start;
  justify-content: center;
}

.type_panel_line3 {
  margin-left: 2%;
  display: flex;
  align-items: flex-start;
  justify-content: center;
}

.dot {
  font-size: 3vw;
  font-weight: bold;
  height: 8vw;
  width: 8vw;
  margin: 2%;
  border: black 0.275vw solid;
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  color: black;
}

.small_dot {
  font-size: 2.5vw;
  font-weight: bold;
  height: 6vw;
  width: 6vw;
  margin: 2%;
  border: black 0.275vw solid;
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  color: black;
}

.xs_dot {
  font-size: 1.5vw;
  font-weight: bold;
  height: 4vw;
  width: 4vw;
  margin: 1.5%;
  border: black 0.275vw solid;
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  color: black;
}

.colored_xs_dot {
  font-size: 1.5vw;
  font-weight: bold;
  height: 4vw;
  width: 4vw;
  margin: 1.5%;
  border: black 0.275vw solid;
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  color: black;
  background-color: #d0dae1;
}

.clicked_dot {
  font-size: 3vw;
  font-weight: bold;
  height: 8vw;
  width: 8vw;
  margin: 2%;
  background-color: rgb(255, 187, 0);
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  color: white;
}

.record_dot {
  font-size: 1.5vw;
  font-weight: bold;
  height: 8vw;
  width: 8vw;
  margin: 2%;
  border: black 0.275vw solid;
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  color: black;
}

.clicked_record_dot {
  font-size: 1.5vw;
  font-weight: bold;
  height: 8vw;
  width: 8vw;
  margin: 2%;
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  color: white;
  background-color: rgb(255, 187, 0);
}

.hr {
  height: 0.2vw;
  background-color: black;
  margin-top: 1%;
  margin-bottom: 1%;
  border-radius: 200px;
  width: 90%;
  display: inline-block;
  align-self: center;
}

.small_hr {
  height: 2px;
  background-color: black;
  margin-top: 1%;
  margin-bottom: 1%;
  border-radius: 200px;
  width: 90%;
  display: inline-block;
  align-self: center;
}

.btn {
  font-size: 1.35vw;
  background-color: #d0dae1;
  margin-left: 2vw;
  width: 8vw;
}

.btn:hover {
    border: #bcc5cb 1px dashed;
}

.display {
  height: 8vw;
  width: 14.5vw;
  margin: 2% 0px;
  display: inline-flex;
  align-items: end;
}

.empty {
  height: 8vw;
  width: 10vw;
  margin: 2.5%;
  display: inline-flex;
  align-items: center;
}

.wide_empty {
  height: 8vw;
  width: 22.5vw;
  margin: 2%;
  display: inline-flex;
  align-items: center;
  padding: 0px;
}
</style>