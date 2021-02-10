<template>
  <div class="home">
    <video-selfie></video-selfie>
  </div>
</template>

<script>
// @ is an alias to /src
import VideoSelfie from "@/components/VideoSelfie.vue";

export default {
  name: "Home",
  components: {
    VideoSelfie
  }
};
</script>
