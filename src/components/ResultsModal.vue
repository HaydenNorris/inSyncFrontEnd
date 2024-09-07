<template>
  <div class="container" v-if="show" @click="close">
    <div class="content">
      <div class="header">
        <img src="@/images/resultsIcon.png" alt="Results Icon" />
        <h2>{{ modalHeading }}</h2>
      </div>
      <p class="objective">
        {{ header }} {{ runningTotal }}
        <span v-if="!showArrows">/ {{ $gameStore.game.potential_score }}</span>
      </p>
      <p class="objective" v-if="!showArrows">{{ overAllScore }}</p>
      <div v-if="showArrows" class="carousel">
        <transition name="fade" mode="out-in">
          <div class="whiteBox" :key="currentIndex">
            <p>
              <b>{{ blocks[currentIndex]?.title }}: </b>{{ blocks[currentIndex]?.body }}
            </p>
            <p><b>Group Guess: </b></p>
            <div class="sliderContainer">
              <p>{{ blocks[currentIndex]?.low_prompt }}</p>
              <slider
                :value="blocks[currentIndex]?.guess_value"
                :max="blocks[currentIndex]?.max_value"
                :disabled="true"
              />
              <p>{{ blocks[currentIndex]?.high_prompt }}</p>
            </div>
            <p>
              <b>{{ blocks[currentIndex]?.player_name }} said: </b>
            </p>
            <div class="sliderContainer">
              <p>{{ blocks[currentIndex]?.low_prompt }}</p>
              <slider
                :value="blocks[currentIndex]?.target_value"
                :max="blocks[currentIndex]?.max_value"
                :disabled="true"
              />
              <p>{{ blocks[currentIndex]?.high_prompt }}</p>
            </div>
            <p><b>Points Earned: </b> {{ blocks[currentIndex]?.score }}</p>
          </div>
        </transition>
        <div v-if="showArrows" class="button-container">
          <button class="prev" @click="prevSlide"><</button>
          <button class="next" @click="nextSlide">></button>
        </div>
      </div>
      <div class="button-container">
        <button v-if="!showArrows" class="rounded-button" @click="playAgain">Play Again</button>
      </div>
    </div>
  </div>
</template>

<script>
import Slider from '@/components/slider.vue'

export default {
  components: {
    Slider
  },
  props: {
    show: {
      type: Boolean,
      default: false
    },
    header: {
      type: String,
      default: ''
    },
    blocks: {
      type: Array,
      default: []
    },
    modalHeading: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      currentIndex: 0,
      showArrows: true
    }
  },
  computed: {
    runningTotal() {
      if (this.blocks.length === 0) {
        return 0
      }
      let totalScore = 0
      let finalIndex = this.showArrows ? this.currentIndex : this.blocks.length - 1
      for (let i = 0; i <= finalIndex; i++) {
        totalScore += this.blocks[i].score
      }
      return totalScore
    },
    overAllScore() {
      let scorePercentage =
        (this.$gameStore.game.score / this.$gameStore.game.potential_score) * 100

      if (scorePercentage < 20) {
        return 'Your current score is like the dark void of space—mysterious but with endless potential for discovery!';
      }
      if (scorePercentage < 40) {
        return 'Your performance is like a distant nebula—there’s room to grow and shine brighter as you explore new horizons.';
      }
      if (scorePercentage < 60) {
        return 'If knowledge were stars, you’d be a burgeoning constellation—showing promise and the potential to light up the night sky.';
      }
      if (scorePercentage < 80) {
        return 'You’re like a bright star in the galaxy—making a significant impact, but with the potential to shine even more brightly.';
      }
      if (scorePercentage < 100) {
        return 'You’re on the edge of a stellar breakthrough—like a planet with the potential for greatness, orbiting just shy of perfection!';
      }
      if (scorePercentage === 100) {
        return 'Congratulations! You’ve reached cosmic perfection—a supernova of brilliance lighting up the entire galaxy!';
      }

      return ''
    }
  },
  methods: {
    nextSlide() {
      if (this.currentIndex < this.blocks.length - 1) {
        this.currentIndex++
      } else {
        this.currentIndex = 0
        this.showArrows = false
      }
    },
    prevSlide() {
      if (this.currentIndex > 0) {
        this.currentIndex--
      } else {
        this.currentIndex = this.blocks.length - 1
      }
    },
    playAgain() {
      this.$router.push('/play')
    }
  }
}
</script>

<style scoped>
.container {
  height: 100vh;
  width: 100vw;
  z-index: 100;
  background-color: rgba(94, 94, 94, 0.5);
  position: absolute;
}

.content {
  height: auto;
  max-width: 700px;
  width: 70vw;
  background-color: white;
  border-radius: 1rem;
  box-shadow: -10px 10px 20px 5px rgba(29, 22, 51, 0.6);
  font-family: 'Arial', 'sans-serif';
  font-size: 1.5rem;
  overflow-y: auto;
  padding-bottom: 2em;
  background-color: #60129d;
}

.header {
  background-color: white;
  padding: 0.5em;
  font-size: 0.9em;
  display: flex;
  align-items: center;
  position: relative;
}

.header img {
  max-width: 50px;
  margin-right: 1em;
  margin-left: 1em;
}

h2 {
  margin: 0;
  font-family: 'Helvetica', sans-serif;
  font-size: 1rem;
  font-weight: 700;
  padding-top: 1.5%;
  color: #2d1b5e;
}

@media (max-width: 600px) {
  .content {
    width: 90vw;
  }
  p {
    padding: 4px;
    margin: 0 auto;
    font-size: 0.95em;
  }
  .whiteBox {
    padding: 10px;
  }
  .objective {
    font-size: 1.2em;
    padding-top: 1em;
  }
}

.whiteBox {
  background-color: white;
  border-radius: 4px;
  width: 70%;
  height: auto;
  display: flex;
  flex-direction: column;
  text-align: center;
  align-items: center;
  justify-content: center;
  margin: 10px auto;
  padding: 20px;
  color: #241451;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
  transition: 0.5s ease-in-out;
}

.carousel {
  position: relative;
  width: 100%;
  overflow: hidden;
  text-align: center;
}

.objective {
  color: white;
  font-size: 1.5em;
  text-align: center;
  line-height: 1.2em;
}

.button-container {
  margin-top: 10px;
  display: flex;
  justify-content: center;
  width: 100%;
}

.prev,
.next {
  background-color: rgba(0, 0, 0, 0.5);
  color: white;
  border: none;
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  padding-left: 1rem;
  padding-right: 1rem;
  cursor: pointer;
  font-size: 2em;
  border-radius: 0.5rem;
  margin: 5px;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.4);
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.7s ease;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}

.prompt_container {
  width: 100%;
  display: flex;
  justify-content: space-between;
  margin-top: 1.5em;
}

.sliderContainer {
  width: 100%;
  border-radius: 4px;
  padding: 1em;
  display: inline-flex;
  font-size: 1em;
}

p {
  color: #2d1b5e;
}

input[type='range']::-webkit-slider-runnable-track {
  background: linear-gradient(to right, rgb(231, 210, 243) 0%, #84369c 100%);
}

input[type='range']::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  height: 4em;
  width: 4em;
  background: url('@/images/starOption.png') center no-repeat;
  background-size: cover;
  cursor: grab;
  position: relative;
  top: 50%;
  transform: translateY(-50%);
}

.sliderContainer p {
  padding: 0.5em;
}
</style>
