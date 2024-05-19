<template>
    <div class="timer-container">
        <div class="timer">{{ hours }}:{{ minutes }}:{{ seconds }}</div>
        <div class="controls">
            <button :class="{ start: !isRunning, stop: isRunning }" @click="$emit('toggle')">{{ isRunning ? 'Stop' :
                'Start'
                }}</button>
            <button class="clear" @click="$emit('clear')">Clear</button>
        </div>
    </div>

</template>

<script>
export default {
    props: ['hours', 'minutes', 'seconds', 'isRunning']
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
</style>
