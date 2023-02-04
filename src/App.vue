<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "000",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "000",
          "name": "Wakeup-Call",
          "status": "failure"
        },
        {
          "slug": "001",
          "name": "Bug-Hunt",
          "status": "success"
        },
      ],
      "pilots": [
        {
          "callsign": "hellfire",
          "alias": "percival",
          "code": "17cf6641-464e-4a0d-8117-4e8938f750de.Percival:17cf6641-464e-4a0d-8117-4e8938f750de//NDL-C-SACRED-DECEMBER",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "the gateless gate"
        },
        {
          "callsign": "script kitten",
          "alias": "alyssa nightingale",
          "code": "5ab5c988-e805-4f8b-914f-d2d6ea2598d1Nightingale.Alyssa:5ab5c988-e805-4f8b-914f-d2d6ea2598d1//NDL-C-SORROW-JUDGE",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "baby steps"
        },
        {
          "callsign": "felony",
          "alias": "Alex Ranger",
          "code": "0563475c-4e92-44af-8f5c-5309fbad3f21Ranger.Alex:0563475c-4e92-44af-8f5c-5309fbad3f21//NDL-C-SOLAR-TEMPLE",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "subpoena"
        },
        {
          "callsign": "banshee",
          "alias": "Selena Korbar",
          "code": "f2da5ed8-32c1-43af-a26d-0e45cc582aeeKorbar.Selena:f2da5ed8-32c1-43af-a26d-0e45cc582aee//NDL-C-SATELLITE-DIVER",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "less goofy"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Mirrorsmoke",
        "headerSubtitle": "Mercenary Company",
        "subheaderTitle": "Union Contract",
        "subheaderSubtitle": "Rapid Response Force - 4A3-11",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
