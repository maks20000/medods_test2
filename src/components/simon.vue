<template>
  <div class="wrapper">
    <div class="simon-wrapper">
      <div
        v-for="(simon, i) in simonItems"
        :key="i"
        :class="[simon.class, { active: simon.active }]"
        @click="simon_click(i)"
      ></div>
    </div>
    <div class="controll">
      <h2>Уровень: {{ lvl }}</h2>
      <button class="btn_start" @click.prevent="start">Старт</button>
      <p>{{ error }}</p>
      <div class="сomplexity">
        <h2>Сложность</h2>
        <div v-for="(complexity_item, i) in сomplexity" :key="i">
          <input
            :id="'c_' + i"
            type="radio"
            :value="complexity_item.value"
            v-model="сomplexity_current"
            @change="reset(1, false)"
          />
          <label :for="'c_' + i">{{ complexity_item.text }}</label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "babel-polyfill";
export default {
  data() {
    return {
      lvl: 1,
      game_state: 0,
      click_count: 0,
      error: "",
      сomplexity_current: 0,
      current_set: [],
      simonItems: [
        { class: "blue", sound: 0, active: false },
        { class: "red", sound: 1, active: false },
        { class: "green", sound: 3, active: false },
        { class: "yellow", sound: 2, active: false }
      ],
      audio: [
        { a: new Audio(require("../assets/sounds/1.mp3")) },
        { a: new Audio(require("../assets/sounds/2.mp3")) },
        { a: new Audio(require("../assets/sounds/3.mp3")) },
        { a: new Audio(require("../assets/sounds/4.mp3")) }
      ],
      сomplexity: [
        { text: "Легкий", value: 0 },
        { text: "Средний", value: 1 },
        { text: "Сложный", value: 2 }
      ],
      timeout: [1.5, 1.0, 0.4]
    };
  },
  methods: {
    async start() {
      if (this.game_state == 0) {
        this.game_state = 1;
        var timeout = this.timeout[this.сomplexity_current];
        let rnd = Math.floor(Math.random() * this.simonItems.length);
        this.current_set.push(rnd);
        for (let i = 0; i < this.current_set.length; i++) {
          this.simonItems[this.current_set[i]].active = true;
          this.audio[this.simonItems[this.current_set[i]].sound].a.play();
          await this.delay(timeout * 1000);
          this.simonItems[this.current_set[i]].active = false;
          await this.delay(300);
        }
        this.game_state = 2;
      }
    },
    simon_click(index) {
      let audio = this.audio[this.simonItems[index].sound].a;
      audio.pause();
      audio.currentTime = 0.0;
      audio.play();
      if (this.game_state == 2) {
        if (this.current_set[this.click_count] === index) {
          this.click_count++;
          if (this.click_count >= this.current_set.length) {
            this.game_state = 1;
            this.reset(this.lvl + 1, true);
          }
        } else {
          this.reset(1, false);
          this.error = "Вы проиграли";
        }
      }
    },
    async reset(lvl, start) {
      this.lvl = lvl;
      this.click_count = 0;
      await this.delay(800);
      this.game_state = 0;
      if (start) {
        this.start();
        return;
      }
      this.current_set = [];
    },
    async delay(time) {
      await new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve();
        }, time);
      });
    }
  }
};
</script>

<style>
.wrapper {
  display: flex;
  justify-content: center;
}
.simon-wrapper {
  display: flex;
  flex-wrap: wrap;
  width: 350px;
  height: 350px;
}
.simon-wrapper > div {
  width: 50%;
  height: 50%;
  opacity: 0.6;
  border: 2px solid transparent;
  box-sizing: border-box;
}

.simon-wrapper > div:active,
.simon-wrapper > div.active {
  opacity: 1;
}

.simon-wrapper > div:hover {
  border: 2px solid #000;
}

.blue {
  background: #1e90ff;
}

.red {
  background: #ff5643;
}

.green {
  background: #bede15;
}

.yellow {
  background: #feef33;
}

.controll {
  margin-left: 50px;
  width: 350px;
}

h2 {
  margin: 0;
}

.controll .btn_start {
  background: #1e90ff;
  margin: 20px 0;
  border: 0;
  padding: 10px 20px;
  border-radius: 5px;
  color: white;
  font-size: 18px;
}

@media screen and (max-width: 768px) {
  .wrapper {
    flex-direction: column;
    align-items: center;
  }

  .controll {
    margin-top: 50px;
    margin-left: 0px;
  }
}
</style>
