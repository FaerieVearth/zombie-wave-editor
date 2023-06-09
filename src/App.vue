<template>
  <div id="app">
    <!-- {{ wavesPlan }} -->
    <div class="waves-editor-container" :key="renderKey">
      <div class="level-selection">
        <div class="import-export">
          <b-button @click="importFile()" variant="success">Import</b-button>
          <b-button @click="exportFile()" variant="success">Export</b-button>
          <b-button @click="copyToClipboard()" variant="success">Copy to Clipboard</b-button>
          <b-alert v-model="showDismissibleAlert" variant="success" dismissible>
            Copied to clipboard!
          </b-alert>
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
      showDismissibleAlert: false,
      originalText: `{
"rewards": {
      "2": {  
              "gold": "350", 
              "gems": "2"
          },
      "3": {  
              "gems": "1"
          },
      "4": {  
              "gold": "1000", 
              "gems": "1"
          },
      "5": {  
              "gems": "1"
          },
      "6": {  
              "gold": "1200", 
              "gems": "1"
          },
      "7": {  
              "gold": "1700", 
              "gems": "2"
          },
      "8": {  
              "gold": "2500", 
              "gems": "3"
          },
      "9": {  
              "gold": "1600", 
              "gems": "1"
          }
  },
  "inapps": {
      "combo": {
          "price": 6.99,
          "gems": 100,
          "gold": 50000,
          "title": "MEGA PACK",
          "badge": "",
          "icon": ""
      },
      "elite": {
          "price": 99.99,
          "gems": 1000000,
          "gold": 0,
          "title": "ELITE PACK",
          "badge": "",
          "icon": ""
      },
      "gems_min": {
          "price": 3.49,
          "gems": 25,
          "gold": 0,
          "title": "25 Gems",
          "badge": "",
          "icon": "inapp_diamonds_1"
      },
      "gems_mid": {
          "price": 4.99,
          "gems": 50,
          "gold": 0,
          "title": "50 Gems",
          "badge": "50%",
          "icon": "inapp_diamonds_2"
      },
      "gems_max": {
          "price": 9.99,
          "gems": 150,
          "gold": 0,
          "title": "150 Gems",
          "badge": "150%",
          "icon": "inapp_diamonds_3"
      },
      "gold_min": {
          "price": 2.49,
          "gems": 0,
          "gold": 10000,
          "title": "10k Gold",
          "badge": "",
          "icon": "inapp_coins_1"
      },
      "gold_mid": {
          "price": 4.99,
          "gems": 0,
          "gold": 25000,
          "title": "25k Gold",
          "badge": "",
          "icon": "inapp_coins_2"
      },
      "gold_max": {
          "price": 9.99,
          "gems": 0,
          "gold": 100000,
          "title": "100k Gold",
          "badge": "200%",
          "icon": "inapp_coins_3"
      }
  },
  "settings": {
      "starting_gold": 0,
      "starting_gems": 0,
      "tower_build_time": 5,
      "reward_timer": 15,
      "reward_gold_show": true,
      "reward_gems_show": true,
      "reward_refill_show": true,
      "ingame_refill_show": true,
      "interstatials": 0,
      "show_powerups_tut_at_wave": 1,
      "show_ads_at_wave": 1,
      "show_teaser_text": "CLAIM",
      "boost_duration": 120,
      "boost_price": 2,
      "boost_bonus": 6,
      "boost_wave": 1,
      "interstitials_enabled": true,
      "interstitials_wave_start": 11,
      "interstitials_wave_step": 6,
      "interstitials_rewarded_skip": 10,
      "menu_boost_show_after_wave": 0,
      "menu_quests_show_after_wave": 2,
      "menu_automerge_show_after_wave": 12,
      "menu_daily_show_after_wave": 6,
      "menu_level_up_show_after_wave": 8,
      "rate_me_popup_after_wave": 10,
      "automerge_reward": 10,
      "automerge_reward_start": 1,
      "automerge_cost_gem": 4,
      "no_more_waves_increase_percent": 0.005
  },
  "map": {
      "0": {  "name": "The Beggining", 
              "terrain": "grass"
          }
      },
  "quests" : {
      "zombie_killed" : {
              "description": "Destroy {0} zombies", 
              "steps" : [
                      {"goal": 100, "gems": 1}, 
                      {"goal": 1000, "gold": 2800},
                      {"goal": 100000, "gems": 10},
                      {"goal": 1000000, "gold": 100000}
                      ]
              },
      "powerup_used" : {
              "description": "Use {0} power-ups", 
              "steps" : [
                  {"goal": 10, "gold": 1000}, 
                  {"goal": 100, "gems": 2},
                  {"goal": 1000, "gems": 10},
                  {"goal": 10000, "gold": 100000}]
              },
      "max_level" : {
              "description": "Merge to tower level {0}", 
              "steps" : [
                  {"goal": 4, "gems": 1}, 
                  {"goal": 8, "gems": 1},
                  {"goal": 15, "gems": 2}]
              },
      "ammo_used" : {
              "description": "Fire {0} of ammo", 
              "steps" : [
                  {"goal": 1000, "gems": 1}, 
                  {"goal": 10000, "gems": 2},
                  {"goal": 100000, "gems": 10},
                  {"goal": 1000000, "gold": 100000}]
              },
      "bosses_killed" : {
              "description": "Destroy {0} boss zombies", 
              "steps" : [
                  {"goal": 1, "gold": 3000}, 
                  {"goal": 10, "gems": 2},
                  {"goal": 100, "gems": 10},
                  {"goal": 1000, "gold": 100000}]
              },
      "crushers_killed" : {
              "description": "Kill {0} big papa zombies", 
              "steps" : [
                  {"goal": 20, "gold": 1500}, 
                  {"goal": 100, "gems": 2},
                  {"goal": 1000, "gems": 10},
                  {"goal": 10000, "gold": 100000}]
              },
      "headshots" : {
              "description": "Make {0} zombie headshots", 
              "steps" : [
                  {"goal": 100, "gems": 1}, 
                  {"goal": 1000, "gems": 2},
                  {"goal": 100000, "gems": 10},
                  {"goal": 50000, "gold": 100000}]
              },
      "wave_reached" : {
              "description": "Complete wave {0}", 
              "steps" : [
                  {"goal": 10, "gems": 1}, 
                  {"goal": 25, "gems": 2},
                  {"goal": 50, "gems": 10},
                  {"goal": 100, "gold": 100000}]
              },
      "coins_spent" : {
              "description": "Spend {0} coins", 
              "steps" : [
                  {"goal": 2800, "gold": 500}, 
                  {"goal": 10000, "gems": 2},
                  {"goal": 100000, "gems": 10},
                  {"goal": 1000000, "gold": 100000}]
              },
      "diamonds_spent" : {
              "description": "Spend {0} gems", 
              "steps" : [
                  {"goal": 10, "gems": 1}, 
                  {"goal": 100, "gems": 2},
                  {"goal": 1000, "gems": 10},
                  {"goal": 10000, "gold": 100000}]
              },
      "towers_upgrades" : {
              "description": "Upgrade towers {0} times", 
              "steps" : [
                  {"goal": 20, "gems": 1}, 
                  {"goal": 50, "gems": 2},
                  {"goal": 150, "gems": 10},
                  {"goal": 250, "gold": 100000}]
              },
      "workers_upgrades" :{
              "description": "Upgrade workers {0} times", 
              "steps" : [
                  {"goal": 10, "gems": 1}, 
                  {"goal": 20, "gems": 2},
                  {"goal": 50, "gems": 10},
                  {"goal": 100, "gold": 100000}]
              }
      },
  "waves": {
      "z1_base": 10,
      "z1_rate": 0.35,
      "z1_min_speed": 0.4,
      "z1_max_speed": 0.7,
      "z2_base": 12,
      "z2_rate": 0.55,
      "z2_min_speed": 0.4,
      "z2_max_speed": 0.7,
      "z3_base": 17,
      "z3_rate": 0.75,
      "z3_min_speed": 0.4,
      "z3_max_speed": 0.7,
      "b1_base": 250,
      "b1_rate": 1.2,
      "b1_min_speed": 0.2,
      "b1_max_speed": 0.3,
      "z4_base": 1,
      "z4_rate": 1,
      "z4_min_speed": 0.5,
      "z4_max_speed": 0.7,
      "z4_area_damage": 20,
      "z4_area_range": 3,
      "z5_base": 50,
      "z5_rate": 1,
      "z5_min_speed": 0.5,
      "z5_max_speed": 0.7,
      "z5_area_damage": 50,
      "z5_area_range": 2,
      "z5_wall_damage": 20,
      "z6_base": 100,
      "z6_rate": 1,
      "z6_min_speed": 0.5,
      "z6_max_speed": 0.7,
      "z6_range": 1,
      "z6_wall_damage": 20,
      "z7_base": 100,
      "z7_rate": 1,
      "z7_min_speed": 0.5,
      "z7_max_speed": 0.7,
      "z7_spawn_min_time": 3,
      "z7_spawn_max_time": 7,
      "z7_spawn_min_count": 1,
      "z7_spawn_max_count": 5,
      "z8_base": 10,
      "z8_rate": 1,
      "z8_min_speed": 1,
      "z8_max_speed": 1.2,
      "z8_berserk_min_time": 3,
      "z8_berserk_max_time": 8,
      "z8_berserk_speed_boost": 5,
      "z8_wall_damage": 10,
      "min_speed": 0.4,
      "max_speed": 0.7,
      "waves_gold": [160,280,367,447,542,571,713,1231,920,1511,
      1468,1332,1246,2060,2475,5289,6103,6917,3400,4941,
      5453,5965,9077,9635,13146,13757,16384,17027,19875,20544,
      2190,5036,5782,6514,11146,11878,13158,14442,2808,7474,8640,9806,10972,15338,16504,17670,18836,23802,4918,7164,7236,8402,9568,10734,11900,15422,16612,17839,19066,20317,21568,22830,24118,25441,26790,28139,27523,25407,26791,24201,51642,47087,41532,34977,27941,29423,18929,6435,7941,10013,80362,78440,76018,5382,13390,15176,16962,19425,21888,24351,102951,110517,112932,121013,134418,64908,7395,18190,22312,38800]

  },
  "waves_plan": {
      "1": [[15, "z1", 1],[15, "z2", 1],[45, "z1", 1],[75, "z1", 2],[90, "z1", 1],[120, "z1", 1],[180, "z1", 1],[240, "z1", 1],[270, "z1", 1],[270, "z2", 1],[285, "z1", 1],[315, "z1", 1],[345, "z1", 2]]                                                                                                  
  },
  "upgrade_prices" : [150,300,500,750,1000,1250,1500,1750,2000,2250,2500,2750,3000,3250,3500,3750,4250,5000,5750,6500,7250,8000,8750,9500,10250,11000,11750,12500,13250,14000,15000,16000,17000,18000,19000,21000,24000,30000,33000,34000,37000,58000,73000,88000,103000,112000,136000,148000,151000,178500,182000,189000,192500,206500,227500,234500,248500,252000,259000,269500,318500,325500,336000,346500,353500,378000,392000,409500,413000,423500,430500,434000,448000,458500,465500,493500,500500,549500,556500,598500,619500,668500,686000,721000,738500,843500,899500,1109500,1193500,1298500,1400000,1417500,1543500,1633500,1672500,1675500,1708500,1753500,3043500],
  "upgrade_gems_prices" : [1,1,1,1,1,1,1,1,1,2,2,2,2,2,2,2,2,2,3,3,3,3,3,3,3,3,3,3,3,4,4,4,4,4,4,4,4,4,5,5,5,5,5,5,5,5,5,5,5,6,6,6,6,6,6,6,6,7,7,7,7,7,7,7,7,7,7,7,7,8,8,8,8,8,8,8,8,8,8,8,8,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,11,11,11,11,11,11,11,11,11,11,11,11,11,11,11,11,11,12,12,12,12,12,12,12,12,12,12,12,12,12,12,12,12,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,13,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,15,16,16,16,16,16,16,16,16,16,16,16,16],
  "daily_rewards" : [
          {"gems": 2},
          {"gold": 8000},
          {"gems": 5},
          {"gold": 25000},
          {"gems": 7},
          {"gold": 45000},
          {"gems": 11}
  ],
  "tower_level_upgrades": [
      4, 7, 11, 15, 22, 28, 37, 45, 55, 65, 77, 88, 99, 115, 130, 145, 161, 179, 199, 225, 250, 271, 292, 330, 360, 400, 450, 500, 600, 700, 777 
  ],
  "upgrades" : {
      "zombie_nuke": {
      "zombie_nuke_number": {
        "upgrade_name": "Nukes",
        "description": "Increase number of nukes from {0} to {1}",
        "start": 4,
        "step": 1,
        "max": 25,
        "currency": "nukes",
        "price_start_index": 1,
        "price_step_index": 1,
        "gems": true
      },
      "zombie_nuke_duration": {
        "upgrade_name": "Duration",
        "description": "Decrease duration from {0}s to {1}s",
        "start": 8,
        "step": -0.5,
        "max": 2,
        "currency": "s",
        "price_start_index": 1,
        "price_step_index": 1,
        "gems": true
      },
      "zombie_nuke_cooldown": {
        "upgrade_name": "Cooldown",
        "description": "Decrease cooldown period from {0}s to {1}s",
        "start": 35,
        "step": -1,
        "max": 5,
        "currency": "s",
        "price_start_index": 3,
        "price_step_index": 2,
        "gems": true
      }
    },
    "tower_frenzy": {
      "tower_frenzy_duration": {
        "upgrade_name": "Duration",
        "description": "Increase duration from {0}s to {1}s",
        "start": 8,
        "step": 1,
        "max": 20,
        "currency": "s",
        "price_start_index": 1,
        "price_step_index": 1,
        "gems": true
      },
      "tower_frenzy_cooldown": {
        "upgrade_name": "Cooldown",
        "description": "Decrease cooldown period from {0}s to {1}s",
        "start": 25,
        "step": -1,
        "max": 5,
        "currency": "s",
        "price_start_index": 1,
        "price_step_index": 1,
        "gems": true
      }
    },
    "battle_drums": {
      "battle_drums_duration": {
        "upgrade_name": "Duration",
        "description": "Increase duration from {0}s to {1}s",
        "start": 12,
        "step": 1,
        "max": 20,
        "currency": "s",
        "price_start_index": 1,
        "price_step_index": 1,
        "gems": true
      },
      "battle_drums_cooldown": {
        "upgrade_name": "Cooldown",
        "description": "Decrease cooldown period from {0}s to {1}s",
        "start": 20,
        "step": -1,
        "max": 5,
        "currency": "s",
        "price_start_index": 1,
        "price_step_index": 1,
        "gems": true
      }
    },
    "zombie_emp": {
      "zombie_emp_duration": {
        "upgrade_name": "Duration",
        "description": "Increase duration from {0}s to {1}s",
        "start": 15,
        "step": 1,
        "max": 30,
        "currency": "s",
        "price_start_index": 1,
        "price_step_index": 1,
        "gems": true
      },
      "zombie_emp_cooldown": {
        "upgrade_name": "Cooldown",
        "description": "Decrease cooldown period from {0}s to {1}s",
        "start": 15,
        "step": -1,
        "max": 3,
        "currency": "s",
        "price_start_index": 1,
        "price_step_index": 1,
        "gems": true
      }
    },
    "tower_start": {
      "tower_start_level": {
        "upgrade_name": "Tower level up",
        "description": "All new towers start as level {0}!",
        "start": 1,
        "step": 1,
        "max": 30,
        "currency": "lvl",
        "price_start_index": 40,
        "price_step_index": 2,
        "gems": false
      }
    },
      "wall": {
          "wall_hp": {
              "upgrade_name": "Wall HP",
              "description": "Increase wall HP from {0} HP to {1} HP",
              "start": 1000,
              "step": 25,
              "max": 50000,
              "currency": "HP",
              "price_start_index": 1,
              "price_step_index": 1,
              "gems": false
          }
      },
      "tower": {
          "tower_range": {
              "upgrade_name": "Tower Range",
              "description": "Increase range of all towers from {0} m to {1} m",
              "start": 42,
              "step": 1,
              "max": 90,
              "currency": "m",
              "price_start_index": 1,
              "price_step_index": 1,
              "gems": true
          },
          "tower_damage": {
              "upgrade_name": "Tower Damage",
              "description": "All towers damage increases from {0} HP to {1} HP",
              "start": 14,
              "step": 1,
              "max": 100,
              "currency": "hp",
              "price_start_index": 0,
              "price_step_index": 1,
              "gems": true
          },
          "tower_accuracy": {
              "upgrade_name": "Tower Accuracy",
              "description": "All towers hit chance increases from {0}% to {1}%",
              "start": 33,
              "step": 1,
              "max": 100,
              "currency": "%",
              "price_start_index": 1,
              "price_step_index": 1,
              "gems": true
          },
          "tower_headshot": {
              "upgrade_name": "Tower Headshot",
              "description": "All towers headshot chance increases from {0}% to {1}%",
              "start": 4,
              "step": 0.25,
              "max": 25,
              "currency": "%",
              "price_start_index": 0,
              "price_step_index": 1,
              "gems": true
          }
      },
      "armory": {
          "workers": {
              "upgrade_name": "No. of workers",
              "description": "Increase number of workers from {0} to {1}",
              "start": 3,
              "step": 1,
              "max": 25,
              "currency": "",
              "price_start_index": 1,
              "price_step_index": 3,
              "gems": true
          },
          "speed": {
              "upgrade_name": "Speed",
              "description": "All workers increase speed from {0}m/sec to {1}m/sec",
              "start": 1,
              "step": 0.25,
              "max": 25,
              "currency": "m/s",
              "price_start_index": 0,
              "price_step_index": 1,
              "gems": true
          },
          "carry": {
              "upgrade_name": "Carry",
              "description": "All workers carry more ammo. From {0} ammo to {1} ammo",
              "start": 2,
              "step": 1,
              "max": 30,
              "currency": "ammo",
              "price_start_index": 2,
              "price_step_index": 3,
              "gems": true
          }
      }   
  },
  "upgrades_v2": {
    "zombie_nuke_number": {
      "upgrade_name": "Nukes",
      "category": "zombie_nuke",
      "description": "Increase number of nukes from {0} to {1}",
      "start": 4,
      "step": 1,
      "max": 25,
      "currency": "nukes",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
    "zombie_nuke_duration": {
      "upgrade_name": "Duration",
      "category": "zombie_nuke",
      "description": "Decrease duration from {0}s to {1}s",
      "start": 8,
      "step": -0.5,
      "max": 2,
      "currency": "s",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
    "zombie_nuke_cooldown": {
      "upgrade_name": "Cooldown",
      "category": "zombie_nuke",
      "description": "Decrease cooldown period from {0}s to {1}s",
      "start": 35,
      "step": -1,
      "max": 5,
      "currency": "s",
      "price_start_index": 3,
      "price_step_index": 2,
      "gems": true
    },
    "tower_frenzy_duration": {
      "upgrade_name": "Duration",
      "category": "tower_frenzy",
      "description": "Increase duration from {0}s to {1}s",
      "start": 8,
      "step": 1,
      "max": 20,
      "currency": "s",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
      "tower_frenzy_cooldown": {
      "upgrade_name": "Cooldown",
      "category": "tower_frenzy",
      "description": "Decrease cooldown period from {0}s to {1}s",
      "start": 25,
      "step": -1,
      "max": 5,
      "currency": "s",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
    "battle_drums_duration": {
      "upgrade_name": "Duration",
      "category": "battle_drums",
      "description": "Increase duration from {0}s to {1}s",
      "start": 12,
      "step": 1,
      "max": 20,
      "currency": "s",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
    "battle_drums_cooldown": {
      "upgrade_name": "Cooldown",
      "category": "battle_drums",
      "description": "Decrease cooldown period from {0}s to {1}s",
      "start": 20,
      "step": -1,
      "max": 5,
      "currency": "s",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
    "zombie_emp_duration": {
      "upgrade_name": "Duration",
      "category": "zombie_emp",
      "description": "Increase duration from {0}s to {1}s",
      "start": 15,
      "step": 1,
      "max": 30,
      "currency": "s",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
    "zombie_emp_cooldown": {
      "upgrade_name": "Cooldown",
      "category": "zombie_emp",
      "description": "Decrease cooldown period from {0}s to {1}s",
      "start": 15,
      "step": -1,
      "max": 3,
      "currency": "s",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
    "tower_start_level": {
      "category": "tower_start",
      "upgrade_name": "Tower level up",
      "description": "All new towers start as level {0}!",
      "start": 1,
      "step": 1,
      "max": 30,
      "currency": "lvl",
      "price_start_index": 40,
      "price_step_index": 2,
      "gems": false
    },

    "wall_hp": {
      "upgrade_name": "Wall HP",
      "category": "wall",
      "description": "Increase wall HP from {0} HP to {1} HP",
      "start": 1000,
      "step": 25,
      "max": 50000,
      "currency": "HP",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": false
    },
    "tower_range": {
      "upgrade_name": "Tower Range",
      "category": "tower",
      "description": "Increase range of all towers from {0} m to {1} m",
      "start": 42,
      "step": 2,
      "max": 90,
      "currency": "m",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
    "tower_damage": {
      "upgrade_name": "Tower Damage",
      "category": "tower",
      "description": "All towers damage increases from {0} HP to {1} HP",
      "start": 14,
      "step": 1,
      "max": 100,
      "currency": "hp",
      "price_start_index": 0,
      "price_step_index": 1,
      "gems": true
    },
    "tower_accuracy": {
      "upgrade_name": "Tower Accuracy",
      "category": "tower",
      "description": "All towers hit chance increases from {0}% to {1}%",
      "start": 33,
      "step": 1,
      "max": 100,
      "currency": "%",
      "price_start_index": 1,
      "price_step_index": 1,
      "gems": true
    },
    "tower_headshot": {
      "upgrade_name": "Tower Headshot",
      "category": "tower",
      "description": "All towers headshot chance increases from {0}% to {1}%",
      "start": 4,
      "step": 0.25,
      "max": 25,
      "currency": "%",
      "price_start_index": 0,
      "price_step_index": 1,
      "gems": true
    },
    "workers": {
      "upgrade_name": "No. of workers",
      "category": "armory",
      "description": "Increase number of workers from {0} to {1}",
      "start": 3,
      "step": 1,
      "max": 25,
      "currency": "",
      "price_start_index": 1,
      "price_step_index": 3,
      "gems": true
    },
    "speed": {
      "upgrade_name": "Speed",
      "category": "armory",
      "description": "All workers increase speed from {0}m/sec to {1}m/sec",
      "start": 1,
      "step": 0.25,
      "max": 25,
      "currency": "m/s",
      "price_start_index": 0,
      "price_step_index": 1,
      "gems": true
    },
    "carry": {
      "upgrade_name": "Carry",
      "category": "armory",
      "description": "All workers carry more ammo. From {0} ammo to {1} ammo",
      "start": 2,
      "step": 1,
      "max": 30,
      "currency": "ammo",
      "price_start_index": 2,
      "price_step_index": 3,
      "gems": true
    }
  },
  "powerups" : {
      "tower_frenzy": {
          "name": "Tower Frenzy",
          "description": "Increase the accuracy so that every shot becomes headshot.",
          "duration": 8,
          "cost": 25000,
          "cooldown": 25
      },
      "battle_drums": {
          "name": "Battle drums",
          "description": "Boost the towers and triple tower damage to zombies.",
          "duration": 12,
          "cost": 10000,
          "cooldown": 20
      },
      "zombie_nuke": {
          "name": "Nuke the Zombies",
          "description": "Send blasts around the base blowing up zombies on its way.",
          "duration": 12,
          "cost": 4000,
          "cooldown": 15
      },
      "zombie_emp": {
          "name": "EMP",
          "description": "Emmits strong electric shockwaves that paralyze zombies.",
          "duration": 15,
          "cost": 0,
          "cooldown": 15
      }
  },
  "expansions": {
      "base_expansion": [3, 1, 4, 2],
      "barricade_expansion": [3, 5, 2, 6],
      "base_expansion_after_wave": 5
  },
  "notifications": {
      "enabled": true,
      "titles": ["Zombies incoming", "Zombie terror", "Surrounded by zombies", "Dooms day is coming"],
     "texts": ["Protect the city", "Our city will fall", "There will be no more living humans", "Protect us please"]}
  }`
    };
  },
  async mounted() {
    this.entireJson = JSON.parse(this.originalText);
    this.wavesPlan = this.entireJson.waves_plan;
  },
  methods: {
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
      this.originalText = content;
      this.entireJson = JSON.parse(content);
      this.wavesPlan = JSON.parse(content).waves_plan;
      this.renderKey += 1;
    },
    async exportFile() {
      const handle = await window.showSaveFilePicker();
      const writable = await handle.createWritable();
      let st =  this.myReplace(this.originalText, JSON.stringify(this.wavesPlan));
      await writable.write(st);
      await writable.close();
    },
    copyToClipboard() {
      let selBox = document.createElement("textarea");
      selBox.style.position = "absolute";
      selBox.style.left = "0";
      selBox.style.top = "0";
      selBox.style.opacity = "0";
      let st =  this.myReplace(this.originalText, JSON.stringify(this.wavesPlan));
      selBox.value = st;
      document.body.appendChild(selBox);
      selBox.focus();
      selBox.select();
      document.execCommand("copy");
      document.body.removeChild(selBox);
      this.showDismissibleAlert = true;
    },
    myReplace(string,replacement) {
      replacement = replacement.substring(1, replacement.length-1);
      var result = string.match(/(?<="waves_plan": {\s+).*?(?=\s+})/gs);
      return string.replace(result, replacement);
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

.alert {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.alert-dismissible {
  padding-right: 1rem !important;
}

.close {
  border-radius: 15px;
  width: 30px;
  height: 30px;
  align-self: end;
}
</style>
