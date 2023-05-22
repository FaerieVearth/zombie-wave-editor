<template>
  <div id="app">
    <!-- {{ wavesPlan }} -->
    <div class="waves-editor-container" :key="renderKey">
      <div class="level-selection">
        <div class="import-export">
          <b-button @click="importFile()" variant="success">Import</b-button>
          <b-button @click="exportFile()" variant="success">Export</b-button>
          <b-button @click="copyToClipboard()" variant="success">Copy to Clipboard</b-button>
        </div>
        <div class="level-button-container">
          <div
            v-for="(value, levelName, index) in wavesPlan"
            v-bind:key="index"
          >
            <div class="button-container">
              <b-button
                v-bind:class="[
                  'level-button',
                  { 'selected-level': levelName === selectedLevel },
                ]"
                @click="setSelectedLevelHandler(levelName)"
              >
                {{ levelName }} / {{ getNumberOfZombies(value) }}
              </b-button>
              <div
                v-bind:class="[
                  'hide-dropdown',
                  { 'show-dropdown': levelName === selectedLevel },
                ]"
              >
                <b-dropdown
                  offset="-130"
                  class="dropdown-icon"
                  split-class="split-styling"
                >
                  <b-dropdown-item @click="duplicateLevel(levelName)"
                    ><div class="duplicate-level">Duplicate +</div>
                  </b-dropdown-item>
                  <b-dropdown-item @click="deleteLevel(levelName)"
                    ><div class="delete-level">Delete!</div>
                  </b-dropdown-item>
                </b-dropdown>
              </div>
            </div>
          </div>
        </div>
      </div>
      <template v-for="(value, levelName, index) in wavesPlan">
        <EditableTable
          v-if="levelName === selectedLevel"
          v-bind:key="index + levelName"
          v-model="wavesPlan[levelName]"
          :fields="fields"
          :levelIndex="index"
        ></EditableTable>
      </template>
      <WavePlayer :waves="wavesPlan[selectedLevel]"></WavePlayer>
    </div>
  </div>
</template>

<script>
import EditableTable from "./components/EditableTable.vue";
import WavePlayer from "./components/WavePlayer.vue";
import wavesPlanJson from "../data/wavesPlan.json";
/* import axios from "axios"; */
export default {
  name: "App",
  components: {
    EditableTable,
    WavePlayer,
  },
  data() {
    return {
      selectedLevel: "1",
      fields: [
        { key: "angle", label: "Angle", type: "angle" },
        {
          key: "unit",
          label: "Unit",
          type: "select",
          options: ["z1", "z2", "z3", "z4", "z5", "z6", "z7", "z8", "b1"],
        },
        { key: "number_of_units", label: "Number of Units", type: "number" },
        { key: "remove", label: "", type: "remove" },
      ],
      wavesPlan: {},
      entireJson: {},
      renderKey: 1,
    };
  },
  async mounted() {
    this.entireJson = wavesPlanJson;
    this.wavesPlan = this.entireJson.waves_plan;
    /*     await this.getJsonRequest(); */
  },
  methods: {
    /*     async getJsonRequest() {
      const catFactsResponse = await axios
        .get(
          "https://data.edenap.com/settings.php?app=and.z2&user_id=r32r32r32r32"
        )
        .catch(function (error) {
          if (error.response) {
            console.log(error.response.data);
            console.log(error.response.status);
            console.log(error.response.headers);
          } else if (error.request) {
            console.log(error.request);
          } else {
            console.log("Error", error.message);
          }
        });
      this.info = catFactsResponse.data;
    }, */
    setSelectedLevelHandler(wave) {
      this.selectedLevel = wave;
      this.$forceUpdate();
    },
    deleteLevel(wave) {
      delete this.wavesPlan[wave];
      this.formatLevels();
      this.selectedLevel = "1";
    },
    getNumberOfZombies(level) {
      let zombieCount = 0;
      for (let i = 0; i < level.length; i++) {
        zombieCount += level[i][2];
      }
      return zombieCount;
    },
    duplicateLevel(wave) {
      let newKey = 1;
      const newObject = {};
      for (var key in this.wavesPlan) {
        newObject[newKey] = JSON.parse(JSON.stringify(this.wavesPlan[key]));
        if (key === wave) {
          newKey++;
          newObject[newKey] = JSON.parse(JSON.stringify(this.wavesPlan[key]));
        }
        newKey++;
      }
      this.wavesPlan = newObject;
    },
    formatLevels() {
      let newKey = 1;
      const newObject = {};
      for (var key in this.wavesPlan) {
        //console.log("Assign: " + newKey + " the value of: " + key);
        newObject[newKey] = JSON.parse(JSON.stringify(this.wavesPlan[key]));
        newKey++;
      }
      this.wavesPlan = newObject;
    },
    async importFile() {
      let fileHandle;
      [fileHandle] = await window.showOpenFilePicker();
      const file = await fileHandle.getFile();
      const content = await file.text();
      this.entireJson = JSON.parse(content);
      this.wavesPlan = this.entireJson.waves_plan;
      this.renderKey += 1;
    },
    async exportFile() {
      const handle = await window.showSaveFilePicker();
      const writable = await handle.createWritable();
      let saveObj = { entireJson: this.entireJson };
      saveObj.entireJson.waves_plan = this.wavesPlan;
      await writable.write(JSON.stringify(saveObj.entireJson));
      await writable.close();
    },
    copyToClipboard() {
      let selBox = document.createElement("textarea");
      selBox.style.position = "fixed";
      selBox.style.left = "0";
      selBox.style.top = "0";
      selBox.style.opacity = "0";
      let saveObj = { entireJson: this.entireJson };
      saveObj.entireJson.waves_plan = this.wavesPlan;
      selBox.value = JSON.stringify(saveObj.entireJson);
      document.body.appendChild(selBox);
      selBox.focus();
      selBox.select();
      document.execCommand("copy");
      document.body.removeChild(selBox);
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Comfortaa:wght@300;400;500&display=swap");

#app {
  font-family: "Comfortaa";
  background-image: linear-gradient(
    to right,
    #3e4936,
    #707c6b 25%,
    #3e4936 50%,
    #707c6b 75%,
    #3e4936 100%
  );
}
.waves-editor-container {
  display: flex;
  height: 100vh;
  width: 100vw;
}

.level-selection {
  display: flex;
  flex-direction: column;
  padding: 2em;
  background-image: linear-gradient(#b0c2b0, #869b86);
  padding: 1rem;
  margin: 1rem;
  border-radius: 15px;
}

.level-button-container {
  padding-right: 10px;
  overflow-x: hidden;
  overflow-y: auto;
  min-height: 500px;
}

.level-button-container::-webkit-scrollbar-track {
  border-radius: 10px;
  background: #b0c2b0;
}

.level-button-container::-webkit-scrollbar {
  border-radius: 10px;
  width: 16px;
  background: #b0c2b0;
}

.level-button-container::-webkit-scrollbar-thumb {
  border-radius: 10px;
  background-color: #657165;
}

.level-button {
  width: 180px;
  margin: 0.2em 0 0.2em 0;
}
.button-container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: nowrap;
}
.selected-level {
  margin-left: 1em;
  background: #bb2d3b !important;
  border-top-right-radius: 0 !important;
  border-bottom-right-radius: 0 !important;
}

.dropdown-icon {
  z-index: 10;
}

.dropdown-icon button {
  background: rgb(196, 194, 194);
  border: rgb(201, 198, 198) 1px solid;
  border-top-left-radius: 0 !important;
  border-bottom-left-radius: 0 !important;
}

.hide-dropdown {
  display: none;
}

.show-dropdown {
  display: block;
}

.delete-level {
  color: #bb2d3b;
  margin: 1em 0 1em 0;
}

.duplicate-level {
  color: green;
  margin: 1em 0 1em 0;
}

.import-export {
  display: flex;
  flex-direction: column;
  gap: 0.2em;
  padding: 1rem;
}
</style>
