<template>
    <div class="timers-list">
        <div class="container-filter-bar">
            <input v-model="filterTag" placeholder="Filtrer par ressource" @input="filterTimers" />
        </div>
        <table class="table-all-timers">
            <thead>
                <tr>
                    <th></th>
                    <th>Début</th>
                    <th>Fin</th>
                    <th>Durée</th>
                    <th>Ressource</th>
                    <th>Note</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(timer, index) in filteredTimers" :key="index">
                    <td>
                        <i class="fa fa-trash" @click="$emit('remove', index)"></i>
                    </td>
                    <td>{{ timer.start }}</td>
                    <td>{{ timer.end }}</td>
                    <td>{{ timer.duration }}</td>
                    <td><input v-model="timer.tag" @input="updateLocalStorage" /></td>
                    <td><input v-model="timer.description" @input="updateLocalStorage" /></td>
                </tr>
            </tbody>
        </table>
    </div>

</template>

<script>
export default {
    props: ['timers'],
    data() {
        return {
            filterTag: ''
        };
    },
    computed: {
        filteredTimers() {
            if (this.filterTag.trim() === '') {
                return this.timers;
            } else {
                const tag = this.filterTag.trim().toLowerCase();
                return this.timers.filter(timer => timer.tag.toLowerCase().includes(tag));
            }
        }
    },
    methods: {
        filterTimers() {
            this.$emit('filter');
        }
    }
};
</script>

<style scoped>
.timers-list {
    background-color: #333;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
    padding: 20px;
}

.timers-list .container-filter-bar {
    display: flex;
    justify-content: center;
    align-items: center;
}

.timers-list .container-filter-bar input {
    background: #e6decf;
    border: none;
    border-radius: 50px;
    box-sizing: border-box;
    color: #000;
    width: 80%;
    padding: 1rem;
}

.timers-list .table-all-timers {
    width: 100%;
    box-sizing: border-box;
    border-spacing: 0;
    margin-top: 2rem;
}

.timers-list .table-all-timers thead {
    background-color: #ECDBBA;
    font-size: 1.3rem;
    color: #000;
}

.timers-list .table-all-timers tbody tr td {
    padding: 1rem 0.3rem;
}

.timers-list .table-all-timers input {
    background: #383838;
    border: none;
    box-sizing: border-box;
    color: #ECDBBA;
    width: 100%;
    padding: 0.4rem;
}

.timers-list .table-all-timers i {
    color: #C84B31;
    cursor: pointer;
}
</style>
