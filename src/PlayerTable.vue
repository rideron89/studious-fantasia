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
                        :class="{ 'center': column != 'Name', 'faded': row[column] == 0 }">{{ row[column] }}</td>
                </tr>
            </tbody>
        </table>

        <player-modal v-if="show_modal" :data="selected_row" @close="handleCloseModal"></player-modal>
    </div>
</template>

<script>
    import PlayerModal from './PlayerModal.vue'

    export default {
        name: 'player-table',

        components: {
            PlayerModal
        },

        props: ['compareList', 'headings', 'rows'],

        computed: {
            encoded_headings: function() {
                return this.headings.map(heading => heading.replace(' ', '<br />'))
            }
        },

        data () {
            return {
                selected_row: {},
                show_modal: false
            }
        },

        methods: {
            handleBodyClick: function(ev) {
                let target = ev.target.dataset.rowIndex

                if (target !== undefined) {
                    this.selected_row = this.rows[target]
                    this.show_modal = true
                }
            },

            handleCloseModal: function() {
                this.show_modal = false
                this.selected_row = {}
            },

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

    .playerTable--head {
        border-bottom: 2px solid #c0c0c0;
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
</style>