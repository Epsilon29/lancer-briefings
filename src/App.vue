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
      "mission_slug": "003",
      "current_md": "",
      "events": "003",
      "missions": [
        {
          "slug": "001",
          "name": "Bug-Hunt",
          "status": "success"
        },
        {
          "slug": "002",
          "name": "Vigilant Gaze",
          "status": "success"
        },
		{
          "slug": "003",
          "name": "Floodgate",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Bear",
          "alias": "Vassili Vladislav",
          "code": "89b50b48-8dab-4406-9ebe-ae46ac2b6d23//NDL-C-LUNAR-CRYPT",
          "corpro": "Harrison Armory",
          "frame": "Genghis",
          "mech": "Black Skies"
        },
        {
          "callsign": "Halloumi",
          "alias": "Katrina Ioannou",
          "code": "c95f7943-0853-440c-ab88-fb2fa2cb7e0b//NDL-C-NULL-CRYSTAL",
          "corpro": "Smith-Shimano Corp",
          "frame": "Death's Head",
          "mech": "Cantucci"
        },
        {
          "callsign": "Mars",
          "alias": "Carter Bracken",
          "code": "9ab0a4fb-6e57-4c06-a8e2-a46d24a687b7//NDL-C-DEEP-CAGE",
          "corpro": "Harrison Armory",
          "frame": "Enkidu",
          "mech": "Deimos"
        },
        {
          "callsign": "Speedbird",
          "alias": "Maxwell Faulkner",
          "code": "71a580d6-6702-4aae-b302-b0b7f6e82f16//NDL-C-NULL-STELE",
          "corpro": "IPS-N",
          "frame": "Lancaster",
          "mech": "Spitfire"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "LANDMARK COLONIAL",
        "headerSubtitle": "Corporate OPS",
        "subheaderTitle": "Crisis Response Team",
        "subheaderSubtitle": "Crisis Red",
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
