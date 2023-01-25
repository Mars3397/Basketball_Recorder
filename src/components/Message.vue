<template>
    <!-- Button exit and its warning modal -->
    <button type="button" class="btn exit-btn" data-bs-toggle="modal" data-bs-target="#exit">Exit</button>
    <div class="modal fade" id="exit" tabindex="-1" aria-labelledby="exitLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body" style="font-weight: bold">
                    Are you sure to exit?<br>
                    You cannot recover data after exit !!
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn" data-bs-dismiss="modal">Cancel</button>
                    <button type="button" class="btn" data-bs-dismiss="modal" @click="exit">Exit</button>
                </div>
            </div>
        </div>
    </div>
    <!-- Message Panel -->
    <button v-if="records.length == 0" class="message" type="button"  data-bs-toggle="modal" data-bs-target="#recent">
        Record Message Panel
    </button>
    <button v-if="records.length > 0" class="message" type="button"  data-bs-toggle="modal" data-bs-target="#recent">
        {{ records[records.length - 1].id }} 號球員 {{ convert_type(records[records.length - 1].type) }} +1
    </button>
    <div class="modal fade" id="recent" tabindex="-1" aria-labelledby="exitLabel" aria-hidden="true">
        <div class="modal-dialog modal-dialog-scrollable">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Records History</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <div v-for="record in records" :key="record" class="records_list">
                        <input :id="record.index" @change="record.selected = !record.selected" class="checkbox" type="checkbox">
                        <label :for="record.index">
                            {{ record.id }} 號球員 {{ convert_type(record.type) }} +1
                        </label>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn" @click="delete_record">Delete</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    emits: ['exit', 'delete'],
    props: {
        records: {
        type: Array,
        required: true 
        }
    }, 
    methods: {
        convert_type(type) {
            if (type == "assist") return "助攻"
            else if (type == "steal") return "抄截"
            else if (type == "block") return "阻攻"
            else if (type == "foul") return "犯規"
            else if (type == "turnover") return "失誤"
            else if (type == "point_2") return "2分"
            else if (type == "point_2_miss") return "2分不進"
            else if (type == "point_3") return "3分"
            else if (type == "point_3_miss") return "3分不進"
            else if (type == "point_1") return "罰球"
            else if (type == "point_1_miss") return "罰球不進"
            else if (type == "o_rebound") return "進攻籃板"
            else if (type == "d_rebound") return "防守籃板"
        },
        exit() {
            this.$emit('exit')
        }, 
        delete_record() {
            var delete_records_index = [], delete_records = []
            for (var record in this.records) {
                if (this.records[record]['selected']) {
                    delete_records_index.push(record)
                    delete_records.push(this.records[record])
                }
            }
            while (delete_records_index.length) {
                this.records.splice(delete_records_index.pop(), 1)
            }
            this.$emit('delete', delete_records)
        }
    },
}
</script>

<style scoped>
    label {
        text-align: center;
    }

    .checkbox {
        margin-top: 2%;
        margin-right: 5%;
    }

    .records_list {
        padding: 1%;
        width: 45%;
        display: flex;
        margin-left: auto;
        margin-right: auto;
    }

    .message {
        width: 25%;
        border: 1px black solid;
        border-radius: 2%;
        padding: 5px 0px;
        margin: 0px auto;
    }

    .exit-btn {
        position: absolute;
        right: 12%;
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
</style>