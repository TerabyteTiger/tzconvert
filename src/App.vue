<script setup>
import { ref, computed } from "vue";
import moment from "moment-timezone";

let startDate = moment().format("YYYY-MM-DDTHH:mm");
// data
const copiedFlag = ref(0);
const dtString = ref(startDate);
const evtName = ref("");
const srch = new URLSearchParams(window.location.search);
const srchDt = srch.get("dt");
const srchEvt = srch.get("name");
const sharedDt = moment.utc(srchDt, "X").local().format("YYYY-MM-DDTHH:mm");
const tz = moment.tz.guess();

// functions
const dtFormatted = computed(() => {
  // save to utc for reformatting later
  return moment(dtString.value).utc().format("X");
});

const urlString = computed(() => {
  return (
    window.location.origin +
    `/?dt=${dtFormatted.value}&name=` +
    encodeURIComponent(evtName.value)
  );
});

const baseURL = computed(() => {
  return window.location.origin;
});

const localTz = computed(() => {
  return moment(srchDt, "X").format("LLLL");
});

function copyURL() {
  navigator.clipboard.writeText(urlString.value);
  copiedFlag.value = 1;
}
</script>

<template>
  <div class="text-2xl container">
    <div v-if="srchDt">
      <p>
        <span v-if="srchEvt"
          >{{ srchEvt }} will start at {{ localTz }} for your timezone ({{
            tz
          }})</span
        ><span v-else
          >The event will start at {{ localTz }} for your timezone ({{
            tz
          }})</span
        >
      </p>
      <a :href="baseURL">Share a Date/time with someone</a>
    </div>
    <div v-else>
      <!-- Move sharing stuff in here -->
      <p>Sharing dates and times is hard, especially across Timezones.</p>
      <p class="italic">What if it was easier?</p>

      <label for="">First, what's the name of your event:</label>
      <br />
      <input
        type="text"
        v-model="evtName"
        class="text-2xl p-2 rounded-xl my-4"
      />
      <br />
      <label for="pickedDatetime"
        >Second, when does it start (your timezone):</label
      >
      <br />
      <input
        type="datetime-local"
        name="pickedDatetime"
        id="pickedDatetime"
        v-model="dtString"
        class="text-2xl p-2 rounded-xl mt-4"
      />
      <br />

      <p>
        That's it! Click this button and share the link with whoever needs to
        know when the event will start!
      </p>
      <button
        class="bg-blue-700 text-white hover:bg-blue-900"
        @click="copyURL()"
      >
        Share with others!
      </button>
      <p v-if="copiedFlag" class="bg-green-200 rounded">Link copied!</p>
    </div>
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
