<template>
  <Timer :hours="hours" :minutes="minutes" :seconds="seconds" :isRunning="isRunning" @toggle="toggleTimer"
    @clear="clearTimer" />
  <TimersList :timers="filteredTimers" @remove="removeTimer" @filter="filterTimers" />
</template>

<script>
import Timer from './components/Timer.vue';
import TimersList from './components/TimersList.vue';

export default {
  data() {
    return {
      hours: '00',
      minutes: '00',
      seconds: '00',
      interval: null,
      startTime: null,
      elapsedTime: 0,
      timers: JSON.parse(localStorage.getItem('timers')) || [],
      isRunning: false,
      filterTag: '',
      filteredTimers: []
    };
  },
  mounted() {
    this.filterTimers();
  },
  methods: {
    toggleTimer() {
      if (this.isRunning) {
        this.stopTimer();
      } else {
        this.startTimer();
      }
    },
    startTimer() {
      this.isRunning = true;
      this.startTime = new Date() - this.elapsedTime;
      this.interval = setInterval(() => {
        const now = new Date();
        this.elapsedTime = now - this.startTime;
        this.seconds = String(Math.floor((this.elapsedTime / 1000) % 60)).padStart(2, '0');
        this.minutes = String(Math.floor((this.elapsedTime / 60000) % 60)).padStart(2, '0');
        this.hours = String(Math.floor(this.elapsedTime / 3600000)).padStart(2, '0');
      }, 1000);
    },
    stopTimer() {
      this.isRunning = false;
      clearInterval(this.interval);
      const endTime = new Date();
      const duration = this.elapsedTime;
      const formattedDuration = `${String(Math.floor(duration / 3600000)).padStart(2, '0')}:${String(Math.floor((duration / 60000) % 60)).padStart(2, '0')}:${String(Math.floor((duration / 1000) % 60)).padStart(2, '0')}`;
      this.timers.push({
        start: new Date(this.startTime).toLocaleTimeString(),
        end: endTime.toLocaleTimeString(),
        duration: formattedDuration,
        description: '',
        tag: ''
      });
      this.updateLocalStorage();
      this.filterTimers();
      this.elapsedTime = 0;
    },
    clearTimer() {
      if (this.interval) {
        clearInterval(this.interval);
        this.interval = null;
      }
      this.isRunning = false;
      this.hours = '00';
      this.minutes = '00';
      this.seconds = '00';
      this.elapsedTime = 0;
    },
    updateLocalStorage() {
      localStorage.setItem('timers', JSON.stringify(this.timers));
    },
    removeTimer(index) {
      this.timers.splice(index, 1);
      this.updateLocalStorage();
      this.filterTimers();
    },
    filterTimers() {
      if (this.filterTag.trim() === '') {
        this.filteredTimers = this.timers;
      } else {
        const tag = this.filterTag.trim().toLowerCase();
        this.filteredTimers = this.timers.filter(timer => timer.tag.toLowerCase().includes(tag));
      }
    }
  },
  watch: {
    timers: {
      handler() {
        this.updateLocalStorage();
        this.filterTimers();
      },
      deep: true
    }
  },
  components: {
    Timer,
    TimersList
  }
};
</script>