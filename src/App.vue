<template>
  <div class="flex flex-col h-screen w-full bg-gray-900">
    <div class="flex flex-row justify-center items-center my-8 h-screen">
      <div v-for="(item,index) in items" :key="index"
      class="absolute mx-2 rounded-lg bg-gray-900"
      style="width:350px;height:580px;transition: transform 1s"
      :style="index == indexSelected ? `transform: translate(${380 * (index + difference)}px) scale(0.9)`
      :`transform: translate(${280 * (index + difference)}px) scale(0.4);cursor:pointer`"
      @click="index != indexSelected ? selectSlide(index) : ''">


        <div class="bg-contain bg-no-repeat h-full rounded-lg">
          <div class="h-full">
            <img :src="index == indexSelected ? item.images[i].url: item.images[0].url" class="h-full rounded-lg">
          </div>

          <div class="w-full pt-4 absolute top-0" v-if="index == indexSelected">
            <div class="w-11/12 flex flex-row m-auto">
              <div class="w-full rounded-lg mr-2 relative h-auto"
                v-for="(el,indexo) in item.images.length" :key="indexo">
                <div class="absolute w-full rounded-lg"
                  style="height:4px;background-color:rgba(255,255,255,.35)">

                </div>
                <div class="absolute w-full rounded-lg"
                style="height:4px;background-color:white"
                :style="indexo == i ? `width : ${percent}%` : i > indexo ? `width : 100%` : `width : 0%`">
                
                </div>
                
              </div>
            </div>

            <div class="flex flex-row w-11/12 mt-4 m-auto">
              <div class="flex justify-start items-center w-1/2">
                <div style="width:35px;height:35px">
                  <img :src="item.picture" class="rounded-full">
                </div>
                <div class="ml-4">
                  <p class="text-sm	text-white font-semibold">{{item.username}}</p>
                </div>
              </div>

              <div class="flex justify-end items-center w-1/2">
                  <svg class="cursor-pointer" @click="isPaused ? startMyOperation(): stopMyOperation()" fill="#ffffff" height="16" viewBox="0 0 48 48" width="16">
                    <path v-if="isPaused" d="M9.6 46.5c-1 0-2-.3-2.9-.8-1.8-1.1-2.9-2.9-2.9-5.1V7.3c0-2.1 1.1-4 2.9-5.1 1.9-1.1 4.1-1.1 5.9 0l30.1 17.6c1.5.9 2.3 2.4 2.3 4.1 0 1.7-.9 3.2-2.3 4.1L12.6 45.7c-.9.5-2 .8-3 .8z"></path>
                    <path v-else d="M15 1c-3.3 0-6 1.3-6 3v40c0 1.7 2.7 3 6 3s6-1.3 6-3V4c0-1.7-2.7-3-6-3zm18 0c-3.3 0-6 1.3-6 3v40c0 1.7 2.7 3 6 3s6-1.3 6-3V4c0-1.7-2.7-3-6-3z"></path>
                  </svg>
              </div>
            </div>


          </div>

          <div v-else>
            <div class="absolute top-1/2 left-1/2" style="z-index:99;transform:translate(-50% ,-50%) scale(2.5);transition:transform 1s">
              <div class="flex flex-col items-center">
                <div style="width:50px;height:50px" class="rounded-full border-2 border-blue-500">
                  <img :src="item.picture" class="rounded-full">
                </div>
                <div class="mt-2">
                  <p class="text-sm	text-white font-semibold">{{item.username}}</p>
                </div>
              </div>
            </div>

            <div class="absolute inset-0 rounded-lg"
            style="z-index:10;background:-webkit-gradient(linear,left top,left bottom,from(rgba(38,38,38,.6)),to(rgba(38,38,38,0)))">

            </div>

          </div>
        </div>

        <div v-if="index == indexSelected" class="absolute top-1/2" style="left:-45px">
            <i @click="prev(index)" class="fa fa-chevron-circle-left hover:text-gray-200 text-gray-500 cursor-pointer"
            style="font-size:36px"></i>
        </div>
        <div v-if="index == indexSelected" class="absolute top-1/2" style="right:-45px">
            <i @click="next(index)" class="fa fa-chevron-circle-right hover:text-gray-200 text-gray-500 cursor-pointer"
            style="font-size:36px"></i>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import { onMounted, ref } from 'vue'
import axios from 'axios';

export default {
  name: 'App',
  components: {
    
  },

  setup(){

    const indexSelected = ref(0);
    const difference = ref(0);
    const items = ref([]);
    const i = ref(0);
    const percent = ref(0);
    const timer = ref(0);
    const progress = ref(0);
    const duration = ref(6000);
    const interval = ref(0);
    const isPaused = ref(false);
    const newDuration = ref(0);
    const pausePercent = ref(0);

    function selectSlide(index) {
      
      difference.value += indexSelected.value - index;
      indexSelected.value = index;
      i.value = 0;

      resetPlay();
    }

    function getItems() {
      
      axios.get('./items.json')
          .then((response) => {

            items.value = response.data.items;

            play();
          })
     }

    function next(index){

      if (indexSelected.value >= items.value.length - 1 && i.value >= items.value[indexSelected.value].images.length - 1) {
        
        setTimeout(() => {

          difference.value = 0;
          indexSelected.value = 0;
          i.value = 0;

        }); // without delay
        

      }
      else if(i.value >= items.value[indexSelected.value].images.length - 1){

        setTimeout(() => {

          difference.value += index - (index + 1);
          indexSelected.value++;
          i.value = 0;

        }); // without delay
        
      }
      else{
        i.value++;
      }

      resetPlay();
      
    }

    function prev(index) {

      if (indexSelected.value <= 0 && i.value <= 0) {

        i.value = 0;

      }
      else if (i.value <= 0) {
        
        setTimeout(() => {

          difference.value += index - (index - 1);
          indexSelected.value--;
          i.value = 0;

        }); // without delay
      }
      else{
        i.value--;
      }

      resetPlay();
      
    }

    function going() {
      let time = new Date().getTime();

      if (newDuration.value > 0) {

        percent.value = pausePercent.value + Math.floor(100 * (time - timer.value) / duration.value);
      }else{
        
        percent.value = Math.floor(100 * (time - timer.value) / duration.value);
      }

      
    }

    function autoPlay() {
      
      if (indexSelected.value >= items.value.length - 1 && i.value >= items.value[indexSelected.value].images.length - 1) {
        
          stopMyOperation();
          return;

      }
      else if(i.value >= items.value[indexSelected.value].images.length - 1){

          difference.value += indexSelected.value - (indexSelected.value + 1);
          indexSelected.value++;
          i.value = 0;
        
      }
      else{
        i.value++;
      }

      resetPlay();
    }

    function play() {

      timer.value = new Date().getTime();
      
      progress.value = setInterval(going, duration.value / 100);

      if (newDuration.value > 0) {

        interval.value = setInterval(autoPlay, newDuration.value);

      }else{

        interval.value = setInterval(autoPlay, duration.value);
      }

      
    }

    function stopMyOperation() {
      
      isPaused.value = true;
      pausePercent.value = percent.value;
      clearInterval(progress.value);
      clearInterval(interval.value);

      newDuration.value = duration.value - (pausePercent.value * duration.value / 100);

    }

    function startMyOperation() {
      
      isPaused.value = false;

      play();
    }


    function resetPlay() {

      if (isPaused.value) {

        isPaused.value = false;
      }

      percent.value = 0;

      newDuration.value = 0;
      
      clearInterval(interval.value);
      clearInterval(progress.value);
      play();

    }

    onMounted(() => {

      getItems();

      

    })

    return{
      
      indexSelected,selectSlide,difference,items,
      getItems,next,prev,i,percent,going,play,
      timer,progress,duration,resetPlay,interval,autoPlay,
      isPaused,stopMyOperation,startMyOperation,newDuration,
      pausePercent

    }
  }

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

body{
  overflow-x: hidden;
}


</style>
