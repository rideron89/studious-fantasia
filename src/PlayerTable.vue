<template>
    <div class="playerTable--wrapper">
        <table class="playerTable">
            <thead class="playerTable--head">
                <tr>
                    <th v-for="(column, index) in encoded_headings" v-html="column" :key="index"></th>
                </tr>
            </thead>
            <tbody class="playerTable--body">
                <tr class="playerTable--row" v-for="(row, index) in rows" :key="index"
                    :class="{ 'selected': compareList.indexOf(row.id) !== -1 }"
                    @click="handleRowClick(row.id)">
                    <td class="playerTable--cell" v-for="(column, column_index) in headings"
                        :key="column_index"
                        :data-row-index="index"
                        :class="{ 'center': column != 'Name', 'faded': row[column] == 0 }">
                        <span v-if="column == 'Team'" :class="['team', column == 'Team' ? row[column] : '']" :title="row[column]"></span>
                        <span v-if="column != 'Team'">{{ row[column] }}</span>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        name: 'player-table',

        props: ['compareList', 'headings', 'rows'],

        data () {
            return {
                show_modal: false
            }
        },

        computed: {
            encoded_headings: {
                cache: false,
                get () {
                    return this.headings.map(heading => heading.replace(' ', '<br />'))
                }
            }
        },

        methods: {
            handleRowClick: function(player_id) {
                this.$emit('toggleComparedPlayer', player_id)
            }
        }
    }
</script>

<style>
    .playerTable {
        border-collapse: collapse;
        width: 100%;
    }

    .playerTable--wrapper {
        overflow: auto;
    }

    .playerTable--head {
        border-bottom: 2px solid #c0c0c0;
        vertical-align: bottom;
    }

    .playerTable--row {
        border-bottom: 1px solid #d4d4d4;
    }

    .playerTable--row:nth-child(even) {
        background: #eaf5fb;
    }

    .playerTable--row.selected {
        background: #b6f591;
        border-color: #9fd67f;
    }

    .playerTable--cell {
        height: 50px;
        min-width: 60px;
    }

    .playerTable--cell.center {
        text-align: center;
    }

    .playerTable--cell.faded {
        opacity: 0.55;
    }

    .playerTable--cell:nth-child(1) {
        padding: 0 10px;
    }

    .playerTable--cell .team {
        background-image: url(./assets/nfl_logos.jpg);
        background-position: 0px 0px;
        background-repeat: no-repeat;
        display: block;
        height: 29px;
        width: 44px;
    }

    .playerTable--cell .team.BUF { background-position: 0px 0px; }
    .playerTable--cell .team.BAL { background-position: 0px -29px; }
    .playerTable--cell .team.TEN { background-position: 0px -58px; }
    .playerTable--cell .team.KAN { background-position: 0px -87px; }
    .playerTable--cell .team.PHI { background-position: 0px -116px; }
    .playerTable--cell .team.CHI { background-position: 0px -145px; }
    .playerTable--cell .team.NOR { background-position: 0px -174px; }
    .playerTable--cell .team.SEA { background-position: 0px -203px; }

    .playerTable--cell .team.NYJ { background-position: -44px 0px; }
    .playerTable--cell .team.CLE { background-position: -44px -29px; }
    .playerTable--cell .team.HOU { background-position: -44px -58px; }
    .playerTable--cell .team.LAC { background-position: -44px -87px; }
    .playerTable--cell .team.NYG { background-position: -44px -116px; }
    .playerTable--cell .team.MIN { background-position: -44px -145px; }
    .playerTable--cell .team.CAR { background-position: -44px -174px; }
    .playerTable--cell .team.LAR { background-position: -44px -203px; }

    .playerTable--cell .team.NWE { background-position: -88px 0px; }
    .playerTable--cell .team.CIN { background-position: -88px -29px; }
    .playerTable--cell .team.JAC { background-position: -88px -58px; }
    .playerTable--cell .team.DEN { background-position: -88px -87px; }
    .playerTable--cell .team.WAS { background-position: -88px -116px; }
    .playerTable--cell .team.GNB { background-position: -88px -145px; }
    .playerTable--cell .team.ATL { background-position: -88px -174px; }
    .playerTable--cell .team.ARI { background-position: -88px -203px; }

    .playerTable--cell .team.MIA { background-position: -132px 0px; }
    .playerTable--cell .team.PIT { background-position: -132px -29px; }
    .playerTable--cell .team.IND { background-position: -132px -58px; }
    .playerTable--cell .team.OAK { background-position: -132px -87px; }
    .playerTable--cell .team.DAL { background-position: -132px -116px; }
    .playerTable--cell .team.DET { background-position: -132px -145px; }
    .playerTable--cell .team.TAM { background-position: -132px -174px; }
    .playerTable--cell .team.SFO { background-position: -132px -203px; }
</style>