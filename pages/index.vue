<template>
  <div class="w-full h-full overflow-hidden">
    <div class="text-center">

      <h1 class="title"> <a href="https://chadananda.github.io/compress-test/">Voice Compression Challenge</a> </h1>

      <h3 class="mb-5">
        <a href="/sample.flac" target="_blank" class="opacity-25 hover:opacity-75" download>
        <span class="text-green underline">Source audio: sample.flac (10mb)</span>
        <img src="/download.svg" class="w-10 inline" /></a>
      </h3>


<!-- table -->
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
                            <tr v-for="(sample, index) in examples" :key="`sample-${index}`"
                            class="border-b border-gray-200 hover:bg-gray-100">
                                <td class="py-3 px-6 text-left max-w-xs">
                                    <div class="flex items-center font-medium">
                                     {{sample.desc}}
                                    </div>
                                </td>
                                <td class="py-3 px-auto text-left overflow-x-wrap max-w-md">
                                    <div class="flex text-bold text-xs font-mono break-normal">
                                       {{sample.script}}
                                    </div>
                                </td>
                                <td class="py-3 px-6 text-center">
                                    <div class="flex items-center justify-center">
                                      {{sample.size}} <br>(~{{bytes2mb(sample.size)}})
                                    </div>
                                </td>
                                <td class="py-3 px-6 text-center">
                                    <span class="bg-purple-200 text-purple-600 py-1 px-3 rounded-full text-2xs">
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
      baseSize: 1407140,
      examples: [
        {desc: "Original compressed (non-lossy) FLAC", script: "none", size: 10023703, src:'sample.flac'},

        {desc: "ILM compressed version (Baseline)",  size: 1407140, src:'y8.m4a',
          script: "unknown",
        },

        {desc: "Direct Conversion to M4A",  size: 1397115, src:'test1.m4a',
          script: "ffmpeg -i sample.flac test1.m4a",
        },

        {desc: "Strip extra tags and reduce bitrate",  size: 1073493, src:'test2.m4a',
          script: "ffmpeg -i sample.flac -b:a 96k -map a test2.m4a",
        },

        {desc: "Reduce bitrate further",  size: 719745, src:'test3.m4a',
          script: "ffmpeg -i sample.flac -b:a 64k -map a test3.m4a",
        },

        {desc: "Remove audio channel (fail)",  size: 720175, src:'test4.m4a',
          script: "ffmpeg -i sample.flac -b:a 64k -map a -map_channel 0.0.0 test4.m4a",
        },

        {desc: "VBR (big fail)",  size: 1285662, src:'test7.m4a',
          script: "ffmpeg -i sample.flac -c:a aac -q:a 2 -map a test7.m4a",
        },

        {desc: "Advanced filter (afftdn)",  size: 704199, src:'test5.m4a',
          script: "ffmpeg -i sample.flac -b:a 64k -map a -af afftdn test5.m4a",
        },

        {desc: "Advanced filter (anlmdn)",  size: 666728, src:'test6.m4a',
          script: "ffmpeg -i sample.flac -b:a 64k -map a -af anlmdn test6.m4a",
        },

        {desc: "Filter (anlmdn) plus tighten bitrate",  size: 504189, src:'test8.m4a',
          script: "ffmpeg -i sample.flac -b:a 48k -map a -af anlmdn test8.m4a",
        },

        {desc: "Even tighter bitrate",  size: 487862, src:'test9.m4a',
          script: "ffmpeg -i sample.flac -b:a 46k -map a -af anlmdn test9.m4a",
        },

        {desc: "Dynamic range compression (with a gain boost)",  size: 472156, src:'test10.m4a',
          script: `ffmpeg -i sample.flac -b:a 46k -map a
          -filter_complex "compand=attacks=0:points=-30/-900|-20/-20|0/0|20/20:gain=5"
          test10.m4a`,
        },

        {desc: "More dynamic range compression (sounds good but less compact)",  size: 543837, src:'test11.m4a',
          script: `ffmpeg -i sample.flac -b:a 48k -map a
          -filter_complex "compand=attacks=0:points=-80/-900|-45/-15|-27/-9|0/-7|20/-7:gain=5"
          test11.m4a`,
        },

        // {desc: "Dynamic noise adjustment",  size: 550248, src:'test12.m4a',
        //   script: `ffmpeg -i sample.flac -b:a 48k -map a
        //   -c:v copy -c:a aac -b:a 48k -af "dynaudnorm, afade=t=in:ss=0:d=0.5"
        //   test12.m4a`,
        // },

      ],
    }
  },
  methods: {
    reduction(newSize) {
       let result = (((this.baseSize - newSize) / this.baseSize) * 100).toFixed(2) * -1
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
