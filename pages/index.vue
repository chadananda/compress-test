<template>
  <div class="w-full h-full overflow-hidden">
    <div class="text-center">

      <h1 class="title"> Voice Compression Challenge </h1>

      <h3 class="mb-5">
        <a href="/sample.flac" target="_blank" class="opacity-25 hover:opacity-75" download>
        <span class="text-green underline">Source audio: sample.flac (10mb)</span>
        <img src="/download.svg" class="w-10 inline" /></a>
      </h3>


<!-- table -->
<div class="overflow-x-hidden">
        <div class="min-w-screen bg-gray-100 flex items-center justify-center font-sans overflow-hidden">
            <div class="w-full lg:w-5/6">
                <div class="bg-white shadow-md rounded my-6">
                    <table class="min-w-max w-full table-auto">
                        <thead>
                            <tr class="bg-gray-200 text-gray-600 uppercase text-sm leading-normal">
                                <th class="py-3 px-6 text-left">Description</th>
                                <th class="py-3 px-6 text-left">Script</th>
                                <th class="py-3 px-6 text-center">Size</th>
                                <th class="py-3 px-6 text-center">Reduction</th>
                                <th class="py-3 px-6 text-center">Listen</th>
                            </tr>
                        </thead>
                        <tbody class="text-gray-600 text-sm font-light">
                          <!-- repeating section  -->
                            <tr v-for="(sample, index) in examples" :key="`sample-${index}`"
                            class="border-b border-gray-200 hover:bg-gray-100">
                                <td class="py-3 px-6 text-left whitespace-nowrap">
                                    <div class="flex items-center font-medium">
                                     {{sample.desc}}
                                    </div>
                                </td>
                                <td class="py-3 px-6 text-left">
                                    <div class="flex items-center">
                                       {{sample.script}}
                                    </div>
                                </td>
                                <td class="py-3 px-6 text-center">
                                    <div class="flex items-center justify-center">
                                      {{sample.size}}
                                    </div>
                                </td>
                                <td class="py-3 px-6 text-center">
                                    <span class="bg-purple-200 text-purple-600 py-1 px-3 rounded-full text-xs">
                                      {{reduction(sample.size)}}%
                                    </span>
                                </td>
                                <td class="py-3 px-6 text-center overflow-hidden">
                                    <div class="flex item-center justify-center">
                                        <div class="w-4 mr-2 transform hover:text-purple-500 hover:scale-110">
                                            <audio class="" controls>
                                               <source :src="sample.src">
                                            </audio>
                                        </div>
                                    </div>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
<!-- end table -->



      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      baseSize: 10023703,
      examples: [
        {desc: "No Lossy Compression, just a .flac file", script: "none", size: 10023703, src:'sample.flac'},
        {desc: "Sample Lossy Compression, just a .flac file", script: "none", size: 5023703, src:'sample.flac'},
      ],
    }
  },
  methods: {
    reduction(newSize) {
       return (((this.baseSize - newSize) / this.baseSize) * 100).toFixed(2)
    },
  }

}
</script>

<style>
/* Sample `apply` at-rules with Tailwind CSS
.container {
@apply min-h-screen flex justify-center items-center text-center mx-auto;
}
*/
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 40px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 22px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}

audio::-webkit-media-controls-timeline-container,
audio::-webkit-media-controls-current-time-display,
audio::-webkit-media-controls-mute-button,
audio::-webkit-media-controls-volume-slider
 {
  display: none !important;
}


</style>
