<template>
  <div class="VideoSelfie">
      <video ref="video" width="360" height="640" autoplay playsinline muted></video>
      <video ref="recordedVideo" width="360" height="640" autoplay playsinline loop></video>
      <div>
        <p>progress: {{Math.floor(progress)}}</p>
        <p>time: {{Math.floor(progress * 15 / 100)}}</p>
      </div>
      
      <div class="ui">
      <div class="progress-bar" :style="{width: `${progressTimeBar}%`}">
        </div>
        <div class="circle-button" @mousedown="recordStory" @mouseleave="stopRecordingStory" @touchstart="recordStory" @touchend="stopRecordingStory">
        </div>
        <button  @click="removeVideo">
        delete
        </button>
        
      </div>
  </div>
</template>

<script>
require('md-gum-polyfill');
export default {
  name: "VideoSelfie",
  data() {
    return {
      progress: '',
      interval: false,
      isRercording: true,
      isReplay: false,
      chunks: null,
      maxRecordingTime: 15,
      progressTimeBar: 0,
    }
  },
  props: {
  },
  mounted() {
        this.init();
  },
  methods: {
    init() {
      const {video} = this.$refs
      navigator.mediaDevices.getUserMedia({ video: true, audio: true }).then((stream) =>{
        video.srcObject = stream;
        this.mediaRecorder = new MediaRecorder(stream);
        this.isRecording = true;
      }, (error) =>{
        this.log = error
      });
    },
    startRecording() {
      
      this.mediaRecorder.start()
      this.progress = this.mediaRecorder.state;
      this.mediaRecorder.ondataavailable = (e) => {
          this.chunks = e.data;
        }
    },
    stopRecording() {
      
      this.mediaRecorder.stop();
      this.mediaRecorder.onstop = (e) => {
          const recorded = window.URL.createObjectURL(this.chunks);
          this.$refs.recordedVideo.src = recorded;
          this.isRecording = false;
          console.log(e);
        }
    },
    removeVideo(){ 
      this.chunks = null
      this.$refs.recordedVideo.src = '';
      this.progressTimeBar = 0;
    },
    recordStory() {
      console.log('mousedown');
      this.startRecording();
      const {maxRecordingTime} = this
      const dt = 10
      let deltaTransform = (100 * dt) / (maxRecordingTime * 1000)
      if (!this.interval) {
        	this.interval = setInterval(() => {
            this.progressTimeBar += deltaTransform
            this.progress = this.progressTimeBar
            if(this.progressTimeBar >= 100) {
              this.stopRecordingStory()
            }
            }, dt);	
      }
    },
    stopRecordingStory() {
      console.log('mouseUp');
      if (this.mediaRecorder.state != 'inactive') {
        this.stopRecording(); 
      }
      clearInterval(this.interval)
      this.interval = false
    },
	}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="stylus" src="@/styles/VideoSelfie.styl">

</style>
