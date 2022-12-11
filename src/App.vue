<template>
  <div class="title">
      <h1>Camera Filter</h1>
  </div>
  <div class="flex-container">
      <div class="polaroid" v-show="camera_visible">
        <video
          autoplay
          muted
          playsInline
          v-bind="camera"
          width="640"
          height="480"
          id="cam"
        />
        <div class="container">
          <button class="button" @click="capture" >Capture</button>
        </div>
      </div>
      <div class="polaroid" v-show="canvas_visible">
        <canvas
          id="canvas"
          width="640"
          height="480"
          />
        <div class="container">

          <span v-for="filter in filters" :key="filter">
            <button @click="runFilter(filter)" class="button">{{filter}}</button>
          </span>

          <div>
            <button class="button-back" @click="back" >Back</button>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
import { onMounted, reactive, ref } from 'vue';
import { applyPresetOnCanvas, clarendon, gingham, aden } from 'instagram-filters';

export default {
  name: 'App',
  setup(){
    const camera = reactive({
      srcObject : null,
      element : null,
    });

    const canvas_visible = ref(false);
    const camera_visible = ref(true);

    const filters = ref(['clarendon', 'gingham', 'aden']);

    const constraint = {
      audio : false,
      video : true,
    }

    const load = async() => {
      try{
        camera.srcObject = await navigator.mediaDevices.getUserMedia(constraint);
        camera.element = document.getElementById('cam');
      }
      catch(e)
      {
        console.log(e);
      }
    }

    onMounted(()=>{
      if(navigator.mediaDevices.getUserMedia||navigator.mediaDevices.webkitGetUserMedia)
      {
        load();
      }else{
        alert("Browser Not Supported");
      }
    });

    const canvas = {
      element : null,
      context : null,
    }

    const capture = () =>{
      canvas.element = document.getElementById("canvas");
      canvas.context = canvas.element.getContext("2d");
      canvas.context.drawImage(camera.element,0,0,640,480);

      canvas_visible.value = true;
      camera_visible.value = false;
    }

    const back = () =>{
      canvas_visible.value = false;
      camera_visible.value = true;
    }

    const runFilter = (filter) =>{
      switch(filter){
        case "clarendon":
          applyPresetOnCanvas(canvas.element, clarendon());
          break;
        case "gingham":
          applyPresetOnCanvas(canvas.element, gingham());
          break;
        case "aden":
          applyPresetOnCanvas(canvas.element, aden());
          break;
      }
    }

    return {
      camera,
      capture,
      canvas_visible,
      camera_visible,
      back,
      filters,
      runFilter
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
  margin-top: 60px;
}

.flex-container {
  display :flex;
  padding : 5px;
  align-items: center;
  justify-content: center;
}

div.polaroid {
  background-color: white;
  padding : 10px;
  box-shadow : 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0 , 0.19);
}

div.container{
  text-align: center;
  padding : 10px 20px;
}

body{
  background: #b0c4de;
}

.button{
  display : inline-block;
  padding : 0.3em 1.2em;
  margin : 0 0.3em 0.3em 0;
  border-radius: 2em;
  box-sizing: border-box;
  text-decoration: none;
  font-weight: 700;
  font-size: 18px;
  color: #2c3e50;
  background-color : #f1c40f;
  text-align : center;
  transition : all 0.2s;
}

.button:hover{
  background-color : #f39c12;
}

.button-back{
  display : inline-block;
  padding : 0.3em 1.2em;
  margin : 0 0.3em 0.3em 0;
  border-radius: 2em;
  box-sizing: border-box;
  text-decoration: none;
  font-weight: 700;
  font-size: 18px;
  color: #2c3e50;
  background-color : #ffffff;
  text-align : center;
  transition : all 0.2s;
}

.button-back:hover{
  background-color : #f39c12;
}

</style>
