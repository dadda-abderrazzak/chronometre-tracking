<template>
  <Timer :hours="hours" :minutes="minutes" :seconds="seconds" :isRunning="isRunning" :isPaused="isPaused" :resources="resources"
    @start="startTimer" @pause="pauseTimer" @stop="stopTimer" @clear="clearTimer" />
  <TimersList :timers="filteredTimers" @remove="removeTimer" @filter="filterTimers" />
</template>

<script>
import Timer from './components/Timer.vue';
import TimersList from './components/TimersList.vue';
import Papa from 'papaparse';

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
      isPaused: false,
      filterResource: '',
      filteredTimers: [],
      resources: []
    };
  },
  mounted() {
    this.loadResources();
    this.filterTimers();
  },
  methods: {
    loadResources() {
      Papa.parse('./resources.csv', {
        download: true,
        header: true,
        complete: (results) => {
          this.resources = results.data.map(row => row.resource);
        }
      });
    },
    startTimer() {
      this.isRunning = true;
      this.isPaused = false;
      this.startTime = new Date() - this.elapsedTime;
      this.interval = setInterval(() => {
        const now = new Date();
        this.elapsedTime = now - this.startTime;
        this.seconds = String(Math.floor((this.elapsedTime / 1000) % 60)).padStart(2, '0');
        this.minutes = String(Math.floor((this.elapsedTime / 60000) % 60)).padStart(2, '0');
        this.hours = String(Math.floor(this.elapsedTime / 3600000)).padStart(2, '0');
      }, 1000);
    },
    pauseTimer() {
      this.isRunning = false;
      this.isPaused = true;
      clearInterval(this.interval);
    },
    stopTimer(resource) {
      if (!resource) {
        alert('Sélectionnez une ressource pour arrêter le timer.');
        return;
      }
      this.isRunning = false;
      this.isPaused = false;
      clearInterval(this.interval);
      const endTime = new Date();
      const duration = this.elapsedTime;
      const formattedDuration = `${String(Math.floor(duration / 3600000)).padStart(2, '0')}:${String(Math.floor((duration / 60000) % 60)).padStart(2, '0')}:${String(Math.floor((duration / 1000) % 60)).padStart(2, '0')}`;
      this.timers.push({
        start: new Date(this.startTime).toLocaleTimeString(),
        end: endTime.toLocaleTimeString(),
        duration: formattedDuration,
        description: '',
        resource: resource,
      });
      this.updateLocalStorage();
      this.filterTimers();
      this.elapsedTime = 0;
      this.hours = '00';
      this.minutes = '00';
      this.seconds = '00';
    },
    clearTimer() {
      if (this.interval) {
        clearInterval(this.interval);
        this.interval = null;
      }
      this.isRunning = false;
      this.isPaused = false;
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
      if (this.filterResource.trim() === '') {
        this.filteredTimers = this.timers;
      } else {
        const resource = this.filterResource.trim().toLowerCase();
        this.filteredTimers = this.timers.filter(timer => timer.resource.toLowerCase().includes(resource));
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