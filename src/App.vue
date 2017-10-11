<template>
    <div id="app">
        <div id="title">
            <img src="./assets/logo.png">

            <h1>asdf</h1>
        </div>

        <div class="container">
            <label>
                <span>Google Sheets Published URL</span>
                <br />
                <input type="text" v-model="sheets_url" />
            </label>

            <hr />

            <label>
                <span>Player Name</span>
                <br />
                <input type="text" v-model="player_query" @input="debounced_query_player" />
            </label>

            <hr />

            <button type="button" v-if="show_compared_players || compare_list.length"
                @click="show_compared_players = !show_compared_players">{{ show_compared_players ? 'Hide' : 'Show' }} Player Comparisons</button>

            <br /><br />

            <player-table :headings="headings" :rows="formatted_query_results"
                @addComparedPlayer="handleAddComparedPlayer"
                @removeComparedPlayer="handleRemoveComparedPlayer"></player-table>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'
    import debounce from 'lodash.debounce'

    import PlayerModal from './PlayerModal.vue'
    import PlayerTable from './PlayerTable.vue'

    export default {
        name: 'app',

        components: {
            PlayerModal,
            PlayerTable
        },

        data () {
            return {
                compare_list: [],
                debounced_query_player: debounce(this.searchForPlayer, 300),
                headings: [],
                player_query: '',
                query_results: [],
                show_compared_players: false,
                sheets_url: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTB4kT0ZieKcXJXRdB1WeZBL2izq32MKu_Jvg5lGODqWlm1zzDGxOarnXXmEGnZvoRj3eNWecAyh8IN/pub?gid=0&single=true&output=csv'
            }
        },

        computed: {
            compared_query_results: function() {
                return this.formatted_query_results.filter(row => this.compare_list.indexOf(row.id) !== -1)
            },

            formatted_query_results: function() {
                let out = this.query_results.map(row => {
                    let obj = {}

                    this.headings.map((heading, index) => {
                        if (heading === 'Name') {
                            obj.id = row[index]
                            obj[heading] = row[index].replace(/\\\S+$/, '')
                        } else {
                            obj[heading] = row[index]
                        }
                    })

                    return obj
                })

                if (this.show_compared_players) {
                    out = out.filter(row => this.compare_list.indexOf(row.id) !== -1)
                }

                out.splice(100)

                return out
            }
        },

        mounted () {
            this.searchForPlayer()
        },

        methods: {
            handleAddComparedPlayer: function(player_id) {
                if (this.compare_list.indexOf(player_id) === -1) {
                    this.compare_list.push(player_id)
                }
            },

            handleRemoveComparedPlayer: function(player_id) {
                this.compare_list = this.compare_list.filter(p => p !== player_id)

                if (this.compare_list.length < 1) {
                    this.show_compared_players = false
                }
            },

            searchForPlayer: function() {
                // only get player names with no numbers and at least 3 characters
                if (/^[\sa-z]*$/.test(this.player_query) === false) {
                    // return
                }

                axios(this.sheets_url).then(response => {
                    let data = response.data.split('\n')
                    let headings = []
                    let rows = data

                    if (this.player_query.length) {
                        rows = data.filter((r, index) => {
                            if (index < 2) {
                                headings.push(r)
                                return false
                            }

                            return r.split(',')[0].toLowerCase().indexOf(this.player_query.toLowerCase()) !== -1
                        })
                    } else {
                        rows.map((r, index) => {
                            if (index < 2) {
                                headings.push(r)
                            }
                        })

                        rows.shift()
                        rows.shift()
                    }

                    if (this.headings.length === 0) {
                        // import the headings rows
                        headings.map(row => {
                            row.split(',').map((column, index) => {
                                if (this.headings[index]) {
                                    this.headings[index] += ' ' + column.replace(/\r/g, '')
                                } else {
                                    this.headings[index] = column.replace(/\r/g, '')
                                }
                            })
                        })
                    }

                    this.query_results = rows.map(row => row.replace(/\r/g, '').split(','))
                })
            }
        }
    }
</script>

<style>
    @import url('https://fonts.googleapis.com/css?family=Nunito:400,700');

    *, *:after, *:before {
        box-sizing: border-box;
    }

    a {
        color: #42b983;
    }

    body {
        margin: 0;
        padding: 15px;
    }

    h1, h2, h3, h4, h5 {
        margin: 0 auto 20px;
    }

    hr {
        border: 2px solid #eaeaea;
        border-radius: 2px;
        margin: 15px 0 20px;
    }

    input, select, textarea {
        border-radius: 2px;
        border: 1px solid #b0b0b0;
        font: inherit;
        padding: 6px;
        width: 100%;
    }

    input[type="checkbox"] {
        height: 15px;
        margin: 0 5px;
        vertical-align: -0.2em;
        width: 15px;
    }

    label {
        margin: 10px 0 20px;
    }

    label input {
        margin: 8px 0 0;
    }

    #app {
        -moz-osx-font-smoothing: grayscale;
        -webkit-font-smoothing: antialiased;
        color: #2c3e50;
        font-family: 'Nunito', Helvetica, Arial, sans-serif;
    }

    #title {
        text-align: center;
    }

    .container {
        margin: 0 auto;
        max-width: 1100px;
    }
</style>
