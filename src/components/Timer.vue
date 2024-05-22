<template>
    <div v-if="!selectedResource" class="ressource-container">
        <select v-model="selectedResource">
            <option value="" disabled>Sélectionner une ressource</option>
            <option v-for="resource in resources" :key="resource" :value="resource">{{ resource }}</option>
        </select>
    </div>
    <div v-else class="timer-container">
        <button class="reset" @click="choseResource()"><i class="fa fa-arrow-left"></i> Retour</button>
        <span class="current-resource">{{ selectedResource }}</span>
        <div class="timer">{{ hours }}:{{ minutes }}:{{ seconds }}:{{ milliseconds }}</div>
        <div class="controls">
            <button class="start" v-if="!isRunning" @click="$emit('start')">{{
                isPaused ? 'Continue' : 'Start' }}</button>
            <button v-if="isRunning" class="pause" @click="$emit('pause')">Pause</button>
            <button class="stop" v-if="isRunning || isPaused" @click="validateStop()">Stop</button>
            <button class="clear" @click="$emit('clear')">Clear</button>
        </div>
    </div>

</template>

<script>
export default {
    props: ['hours', 'minutes', 'seconds', 'milliseconds', 'isRunning', 'isPaused', 'resources'],
    data() {
        return {
            selectedResource: ''
        };
    },
    methods: {
        validateStop() {
            if (!this.selectedResource) {
                alert('Sélectionnez une ressource pour arrêter le timer.');
            } else {
                this.$emit('stop', this.selectedResource);
                this.selectedResource = '';
            }
        },
        choseResource() {
            this.$emit('clear');
            this.selectedResource = '';
        }
    }
};
</script>

<style scoped>
.timer-container {
    padding: 20px;
    border-radius: 5px;
    text-align: center;
    background-color: #333;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
    margin: 3rem 0;
    position: relative;
}

.timer-container .timer {
    font-size: 5rem;
}

.timer-container .controls button {
    border: none;
    padding: 10px 20px;
    margin: 0 10px;
    color: white;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    margin-top: 1rem;
}

.timer-container .controls button.start {
    background-color: #346751;
}

.timer-container .controls button.stop {
    background-color: #C84B31;
}

.timer-container .controls button.clear {
    background-color: #ECDBBA;
    color: #000000;
}

.timer-container .controls button.pause {
    background-color: #6b6b6b;
}

.timer-container .reset {
    position: absolute;
    top: 10px;
    left: 10px;
    background-color: #ECDBBA;
    color: #000000;
    border: none;
    padding: 10px 20px;
    border-radius: 5px;
    cursor: pointer;
}

.timer-container .current-resource {
    font-size: 1.5rem;
    color: #ECDBBA;
    position: absolute;
    top: 10px;
    right: 10px;
}

.ressource-container {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 2rem 0;
}
.ressource-container select {
    padding: 10px;
    border-radius: 5px;
    border: none;
    background-color: #e6decf;
    color: #000;
    width: 250px;
}
</style>
