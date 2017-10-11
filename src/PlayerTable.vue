<template>
    <div class="playerTable--wrapper">
        <table class="playerTable">
            <thead class="playerTable--head">
                <tr>
                    <th><!-- Empty for checkbox input --></th>
                    <th v-for="(column, index) in headings" :key="index">{{ column }}</th>
                </tr>
            </thead>
            <tbody class="playerTable--body">
                <tr class="playerTable--row" v-for="(row, index) in rows" :key="index">
                    <td>
                        <input type="checkbox" :value="row.id" @change="handleSelectRow" />
                    </td>
                    <td class="playerTable--cell" v-for="(column, column_index) in headings"
                        :key="column_index"
                        :data-row-index="index"
                        :class="{ 'center': column != 'Name' }">{{ row[column] }}</td>
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

        props: ['headings', 'rows'],

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

            handleSelectRow: function(ev) {
                let selected = ev.target.checked

                if (selected) {
                    this.$emit('addComparedPlayer', ev.target.value)
                } else {
                    this.$emit('removeComparedPlayer', ev.target.value)
                }
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
        border-bottom: 1px solid #c0c0c0;
    }

    .playerTable--row {
        border-bottom: 1px solid #d4d4d4;
    }

    .playerTable--row:nth-child(even) {
        background: #eaf5fb;
    }

    .playerTable--cell {
        height: 50px;
    }

    .playerTable--cell.center {
        text-align: center;
    }

    .playerTable--cell:nth-child(2) {
        padding-right: 25px;
    }
</style>