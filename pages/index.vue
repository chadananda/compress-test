<template>
<div class="w-full h-full overflow-hidden"><div class="text-center">

<!-- title  -->
<h1 class="title"> <a href="https://chadananda.github.io/compress-test/">Voice Compression Challenge</a> </h1>

<!-- list of samples  -->
<h3 class="mb-5" >
  <div class="inline-block" v-for="(smp, index) in allsamples" :key="`samp_${index}`">
    <span class="" v-if="index>0"> &nbsp; | &nbsp; </span>
    <span class="cursor-pointer" :class="{'underline bold': smp.sample!==currsel}"
          @click="currsel=smp.sample">{{smp.name}}</span>
  </div>
</h3>

<!-- download raw sample  -->
<h3 class="mb-5">
  <b>{{curr.name}}</b> raw:
  <a :href="curr.raw" target="_blank" class="opacity-75 hover:opacity-100" download>
  <span class="text-green underline"> {{curr.raw}}</span></a> ({{bytes2mb(curr.rawsize)}})
  <img src="download.svg" class="w-8 inline" />
</h3>

<!-- start audio from...  -->
<input type="range" min="0" max="870" v-model="startValue" class="align-bottom w-6/12" />
<span class="w-16 text-right inline-block font-mono text-gray-500">{{ startFrom }}</span>

<!-- examples table -->
<div class="overflow-x-hidden min-w-screen">
  <div class="min-w-screen bg-gray-100 flex items-center justify-center font-sans overflow-hidden">
    <div class="w-full lg:w-11/12">
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
            <tr v-for="(sample, index) in examples" ref="sample" :key="index"
              class="border-b border-gray-200 hover:bg-gray-100"
              :class="{'bg-green-200': sample.best}">
              <td class="py-3 px-6 text-left max-w-xs">
                <div class="flex items-center font-medium"> {{sample.desc}} </div>
              </td>
              <td class="py-3 px-auto text-left overflow-x-wrap max-w-md">
                <div class="flex text-bold text-xs font-mono break-normal"> {{sample.script}} </div>
              </td>
              <td class="py-3 px-6 text-center">
                <div class="flex items-center justify-center"> {{sample.size}} <br>(~{{bytes2mb(sample.size)}}) </div>
              </td>
              <td class="py-3 px-6 text-center">
                <span class="bg-purple-200 text-purple-600 py-1 px-3 rounded-full text-2xs"> {{reduction(sample.size)}}% </span>
              </td>
              <td class="py-3 px-6 text-center overflow-hidden">
                <div class="flex item-center justify-center">
                  <div class="w-4 mr-2 transform hover:text-purple-500 hover:scale-110">
                    <audio ref="audio" :key="`${sample.src}`"> <source :src="sample.src"> </audio>
                    <button class="playStop px-3" v-on:click="playAudio(index)"> Play </button>
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


</div></div>
</template>



<script>
import {allsamples, allexamples} from "~/components/samples.js"

export default {
  data() {
    return {
      startValue: 0,
      currsel: 'db', allsamples, allexamples
    }
  },
  computed: {
    startFrom: {
      get: function() {
        let v = this.startValue / 10
        if (this.startValue % 10 === 0) return v.toString() + '.0'
        return v
      },
    },

    curr: function() {
      return this.allsamples.filter(sam => sam.sample===this.currsel)[0]
    },
    other_samples: function() {
      return this.allsamples.filter(item => item.sample !== this.curr.sample)
    },
    examples: function() {
      return this.allexamples.filter(item => item.sample === this.curr.sample)
    }
  },
  methods: {
    setSamples(sample) {
      // set current sample, change examples and change audio list

    },
    reduction(newSize) {
       let result = (((this.curr.baseSize - newSize) / this.curr.baseSize) * 100).toFixed(2) * -1
       if (result > .01) return `+${result}`
         else return `${result}`
    },
    bytes2mb(bytes) {
      if (bytes > 999999) {
        let mb = (bytes/(1024*1024)).toFixed(2)
        return `${mb} mb`
      } else {
         let kb = (bytes/(1024)).toFixed(0)
        return `${kb} kb`
      }

    },
    playAudio(index) {
      // console.log(index)
      // return
      // let index = event.target.getAttribute('data-index')
      if (this.$refs.audio) {
        let audio = this.$refs.audio[index]
        // console.log(audio)
        if (audio.paused == false) audio.pause()
        else {
          this.$refs.audio.forEach(e => e.pause())
          audio.currentTime = this.startFrom
          audio.play()
        }
      }
    }
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
