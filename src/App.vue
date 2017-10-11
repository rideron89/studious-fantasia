<template>
    <div id="app">
        <section id="Title">
            <h1>Studious Fantasia</h1>
        </section>

        <section id="PlayerSearch">
            <div class="inputGroup">
                <label class="inputGroup--label">Search Players:</label>
                <input class="inputGroup--input" type="search" v-model="player_query" @input="debounced_query_player" />
            </div>

            <button type="button" :disabled="compare_list.length < 1"
                @click="show_compared_players = !show_compared_players">{{ show_compared_players ? 'Hide' : 'Show' }} Player Comparisons</button>
        </section>

        <section id="Table">

            <player-table :headings="headings" :rows="formatted_query_results" :compare-list="compare_list"
                @toggleComparedPlayer="handleToggleComparedPlayer"></player-table>

            <div id="Pagination">
                <button type="button" :disabled="query_page < 1"
                    @click="query_page -= 1">Prev</button>
                &nbsp;|&nbsp;
                <button type="button" :disabled="(query_page * 100 + 100) >= query_results.length"
                    @click="query_page += 1">Next</button>
            </div>

            <p>Showing {{ (query_page * 100) }}-{{ (query_page * 100) + formatted_query_results.length }} of {{ query_results.length }}.</p>

            <p>Stats provided by the lovely <a href="https://www.pro-football-reference.com/" target="_blank">Pro Football Reference</a>.</p>
        </section>
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
                query_page: 0,
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

                return out.slice(this.query_page * 100, ((this.query_page + 1) * 100))
            }
        },

        mounted () {
            this.searchForPlayer()
        },

        methods: {
            handleToggleComparedPlayer: function(player_id) {
                if (this.compare_list.indexOf(player_id) === -1) {
                    this.compare_list.push(player_id)
                } else {
                    this.compare_list = this.compare_list.filter(p => p !== player_id)

                    if (this.compare_list.length < 1) {
                        this.show_compared_players = false
                    }
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
    @import url('https://fonts.googleapis.com/css?family=Nunito:600,800');

    *, *:after, *:before {
        box-sizing: border-box;
    }

    a {
        color: #42b983;
    }

    body {
        margin: 0;
    }

    fieldset {
        border: 0;
        margin: 0;
        padding: 0;
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
        border: 0;
        border-bottom: 3px solid #c0c0c0;
        font: inherit;
        padding: 6px;
        transition: all 0.15s ease;
        width: 100%;
    }

    input:focus {
        border-color: #add9f1;
        outline: 0;
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
        color: #444;
        display: flex;
        flex-direction: column;
        font-family: 'Nunito', Helvetica, Arial, sans-serif;
        height: 100vh;
        margin: 0 auto;
        max-width: 1100px;
        overflow: hidden;
    }

    #Pagination {
        margin: 20px 0 0;
        text-align: center;
    }

    #PlayerSearch {
        flex: 60px 0 0;
        margin: 0;
        padding: 0 15px 20px;
    }

    #Table {
        overflow: auto;
    }

    #Title {
        display: flex;
        align-items: center;
        flex: 70px 0 0;
        text-align: center;
    }

    #Title h1 {
        margin: 0 auto;
    }

    .inputGroup {
        align-items: center;
        display: flex;
        margin: 0 0 20px;
    }

    .inputGroup--label {
        flex: 0 0 auto;
        margin: 0;
        padding-right: 25px;
    }

    .inputGroup--input {
        flex: 1;
    }
</style>
