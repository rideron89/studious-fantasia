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

            <table v-if="formatted_query_results.length">
                <thead>
                    <tr>
                        <th v-for="(column, index) in headings" :key="index">{{ column }}</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(row, index) in formatted_query_results" :key="index">
                        <td v-for="(column, column_index) in headings" :key="column_index"
                            :class="{ 'center': column != 'Name' }">{{ row[column] }}</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'
    import debounce from 'lodash.debounce'

    export default {
        name: 'app',

        data () {
            return {
                debounced_query_player: debounce(this.searchForPlayer, 300),
                headings: [],
                player_query: 'will',
                query_results: [],
                sheets_url: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTB4kT0ZieKcXJXRdB1WeZBL2izq32MKu_Jvg5lGODqWlm1zzDGxOarnXXmEGnZvoRj3eNWecAyh8IN/pub?gid=0&single=true&output=csv'
            }
        },

        computed: {
            formatted_query_results: function() {
                return this.query_results.map(row => {
                    let obj = {}

                    this.headings.map((heading, index) => {
                        if (heading === 'Name') {
                            obj[heading] = row[index].replace(/\\\S+$/, '')
                        } else {
                            obj[heading] = row[index]
                        }
                    })

                    return obj
                })
            }
        },

        mounted () {
            this.searchForPlayer()
        },

        methods: {
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

                    this.query_results = rows.map(row => row.split(','))
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

    label {
        margin: 10px 0 20px;
    }

    label input {
        margin: 8px 0 0;
    }

    table {
        border-collapse: collapse;
        width: 100%;
    }

    table thead {
        border-bottom: 1px solid #c0c0c0;
    }

    table tbody td {
        height: 50px;
    }

    table tbody td.center {
        text-align: center;
    }

    table tbody tr {
        border-bottom: 1px solid #d4d4d4;
    }

    table tbody tr:nth-child(even) {
        background: #eaf5fb;
    }

    table tbody tr td:first-child {
        padding-right: 25px;
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
