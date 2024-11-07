<script setup lang="ts">
 
  import { ref } from 'vue';
  const state = ref<"stopped"|"running"|"paused"|"restarted">("stopped");
  const timeElapsed = ref(0);
  const interval = ref <number|undefined>(undefined);
  const historytime= ref(0);

  function start(){
    if(state.value !="restarted"){
      state.value="running";
    }
    interval.value= setInterval(()=>{
      timeElapsed.value++;
    },1000);
  }

  function pause(){
    state.value="paused";
    clearInterval(interval.value);
    interval.value=undefined;
  }

  function restart(){
    if (timeElapsed.value > 0) {
      historytime.value = timeElapsed.value; // Record history before resetting
    }
    timeElapsed.value=0;
    if (interval.value!= undefined){
      clearInterval(interval.value)
      interval.value=undefined;
    }
    state.value="restarted";
    start();
  }

  function resume(){
    start();
  }

  function end(){
    historytime.value=timeElapsed.value;
    timeElapsed.value=0;
    clearInterval(interval.value)
    interval.value=undefined;
    state.value='stopped';
  }

  function formatTime(elapsedtime:number){
    const minutes= `0${Math.floor(elapsedtime/60) +""}`.slice(-2);
    const seconds = `0${elapsedtime%60 + ""}`.slice(-2);
    return `${minutes}:${seconds}`
  }

  function message(history:number){
    history= historytime.value;
    const minutes= Math.floor(history/60);
    const seconds = history % 60;
    if(minutes<1){
      return `${seconds} seconds`
    }
    else if(seconds<1){
      return `${minutes} minutes`
    }
    else{
      return `${minutes} minutes ${seconds} seconds`
    }
  }

</script>

<template>
  <span class="message">StopWatch</span>
  <div>
    <div>{{ formatTime(timeElapsed) }}</div>
    <button v-if="state ==='stopped'" @click="start">Start</button>
    <button v-if="state ==='running' || state==='restarted'" @click="pause">Pause</button>
    <button v-if="state==='paused'" @click="resume">Resume</button>
    <button v-if="state ==='paused'" @click="restart">Restart</button>
    <button v-if="state ==='paused'" @click="end">End</button>
  </div>
  <span v-if="(state==='stopped'|| state==='restarted') && historytime>0" class="message">You ended at {{ message(historytime) }} before</span>
</template>

<style scoped>
  .message{
    font-family: 'Digital-7 Mono', 'Courier New', monospace; /* Digital clock-like font */
    font-size: 2rem;
    color: hsla(120, 100%, 50%, 0.877); 
    font-weight: bold;
    margin-bottom: 30px;
  }

  div {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-top: 40px;
  }

  /* Timer Display */
  div > div {
    font-family: 'Digital-7 Mono', 'Courier New', monospace; /* Digital clock-like font */
    font-size: 4rem;
    color: #00FF00; /* Classic green LED color */
    background-color: #000000; /* Black background to mimic LED display */
    border: 2px solid #045618; /* Frame around the display */
    border-radius: 10px;
    padding: 15px 40px;
    text-shadow: 0 0 10px #00FF00, 0 0 20px #00FF00;
    margin-bottom: 30px;
    box-shadow: inset 0 0 20px rgba(13, 255, 0, 0.405);
  }

  /* Buttons */
  button {
    background-color: #000;
    color: white;
    border: none;
    box-shadow: inset 0 0 20px rgb(30, 184, 30);
    padding: 10px 20px;
    margin: 5px;
    border-radius: 5px;
    cursor: pointer;
    font-size: 1rem;
    transition: background-color 0.3s, box-shadow 0.3s;
  }

  button:hover {
    background-color: rgb(3, 102, 3);
    box-shadow: inset 0 0 20px rgb(30, 184, 30);
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  }

  button:disabled {
    background-color: #aaa;
    cursor: not-allowed;
  }
</style>
