<template>
  <div class="purpleBackground">
    <div class="pageContainer">
      <div class="back">
        <button
          class="rounded-button"
          @click="toggleBack"
          aria-label="Exit current page"
        >
          Exit
        </button>
      </div>
      <div class="pageHeading" v-if="!submittedLastClue">
        <h2>Type a Clue</h2>
      </div>
      <div v-if="gotClue && !submittedLastClue" class="slider-wrapper">
        <p class="clueScalePhrase2">{{ capitalizeString(clueObject.low) }}</p>
        <Slider
          :max="clueObject.max_value"
          :value="clueObject.value"
          :disabled="true"
          aria-label="Slider for clue scale"
        />
        <p class="clueScalePhrase1">{{ capitalizeString(clueObject.high) }}</p>
      </div>
      <section v-if="!submittedLastClue">
        <div class="form-group">
          <input
            id="clueField"
            type="text"
            v-model="currentClue"
            placeholder="Enter your clue..."
            maxlength="120"
            aria-label="Input field for entering a clue"
          />
        </div>
        <div class="button-container">
          <button
            class="rounded-button"
            @click="getClue(true)"
            aria-label="Get a new clue scale"
          >
            New Scale
          </button>
          <button
            v-if="clueNumber < maxClues"
            class="rounded-button"
            :disabled="!isClueEntered"
            @click="changeClueNumber(true)"
            aria-label="Go to the next clue"
          >
            Next Clue
          </button>
          <button
            v-if="onLastClue"
            class="rounded-button"
            @click="addClue"
            aria-label="Submit last clue"
          >
            Submit
          </button>
        </div>
      </section>
      <div>
        <p class="waiting" v-if="submittedLastClue">Waiting for other players...</p>
      </div>
      <FunFactsModal
        :show="showModal"
        :header="modalTitle"
        :blocks="modalBlocks"
        @close="toggleModal"
        aria-label="Fun facts modal"
      />
      <h2 v-if="!submittedLastClue">{{ clueNumber }}/3</h2>
    </div>
  </div>
</template>

<script>
import Slider from '@/components/slider.vue'
import FunFactsModal from '@/components/funFactsModal.vue'

export default {
  components: {
    Slider,
    FunFactsModal
  },
  data() {
    return {
      currentClue: '',
      clueNumber: 1,
      clueObject: {},
      gotClue: false,
      maxClues: 3,
      waiting: false,
      submittedLastClue: false,
      showModal: false,
      modalTitle: 'Some fun facts while you wait!',
      modalBlocks: [
        {
          title: 'Waiting for Food',
          body: 'Did you know that the average person spends about 6 months of their life waiting in line for food?'
        },
        {
          title: 'Patience Pays Off',
          body: 'The idea for the Post-it Note came while waiting for glue to dry.'
        },
        {
          title: 'Waiting for Emails',
          body: 'The average office worker spends about 28% of their workweek reading and responding to emails.'
        },
        {
          title: 'Longest Wait',
          body: 'The longest recorded wait for a traffic light to change is 45 minutes!'
        },
        {
          title: 'Corn Flakes',
          body: 'Cornflakes were invented by Dr. John Harvey Kellogg and his brother Will Keith Kellogg while they were waiting for their experiment with wheat to cool. They accidentally discovered that corn could be toasted into flakes instead.'
        },
        {
          title: 'Potato Chips',
          body: 'Potato chips were created by George Crum while waiting for a customer to return a dish of French fries. The customer complained that the fries were too soggy, so Crum decided to make them crispy by frying them thinly.'
        },
        {
          title: 'Teflon',
          body: 'Teflon was discovered by accident by Roy Plunkett while waiting for a reaction in a container of gases. Instead of the expected chemical reaction, he found a slippery substance that became Teflon.'
        },
        {
          title: 'Play-Doh',
          body: 'Play-Doh was originally developed as a wallpaper cleaner, but it was found to be useful as a modeling compound while waiting for it to dry and crack. It was later repurposed as a children’s toy.'
        },
        {
          title: 'Penicillin',
          body: 'Alexander Fleming discovered penicillin while waiting for his bacterial cultures to grow. He noticed that a mold contaminant was killing the bacteria, leading to the discovery of the first antibiotic.'
        }
      ]
    }
  },
  created() {
    if (!this.$userStore.isLoggedIn && this.$gameStore.empty) {
      return
    }
    this.getClue()
  },
  watch: {
    clueObject() {
      this.currentClue = this.clueObject.clue ?? ''
    },
    clueNumber() {
      this.getClue()
      if (this.onLastClue) {
        this.waiting = true
      }
    },
    $gameStore: {
      deep: true,
      handler() {
        if (this.$gameStore?.game?.status === 'GUESSING') {
          this.$router.push('/guess')
        }
      }
    },
    submittedLastClue(newValue) {
      if (newValue) {
        this.showModal = true
      }
    }
  },
  computed: {
    isClueEntered() {
      return this.currentClue.trim() !== ''
    },
    onLastClue() {
      return this.clueNumber === this.maxClues
    }
  },
  methods: {
    async addClue() {
      this.$loading.yes()
      let prompt = this.currentClue.trim()
      if (!prompt) {
        return
      }
      let response = await this.$axios.post(`/clue/${this.clueObject.id}/prompt`, { prompt })
      if (response.status !== 200) {
        alert('Failed to add clue')
      } else if (this.onLastClue) {
        this.submittedLastClue = true
      }
      this.$loading.no()
    },
    async getClue(refresh = false) {
      if (this.$gameStore.empty) {
        return
      }
      this.$loading.yes()
      let url = `/game/${this.$gameStore.game.id}/clue/${this.clueNumber}`
      if (refresh) {
        url = `/clue/${this.clueObject.id}/refresh`
      }
      try {
        let response = await this.$axios.get(url)
        if (response.status !== 200) {
          this.gotClue = false
          throw 'Failed to get clue'
        }
        this.clueObject = response.data
        this.gotClue = true
      } catch (e) {
        this.$error('Failed to get clue!')
      } finally {
        this.$loading.no()
      }
    },
    async changeClueNumber(increment) {
      await this.addClue()
      this.clueNumber = increment ? this.clueNumber + 1 : this.clueNumber - 1
    },
    toggleBack() {
      this.$router.push('/play')
    },
    capitalizeString(string) {
      return string.charAt(0).toUpperCase() + string.slice(1)
    },
    toggleModal() {
      this.showModal = !this.showModal
    }
  }
}
</script>

<style scoped>
.button-container {
  gap: 1rem;
  display: flex;
}

h2,
p {
  color: white;
}

@media (max-width: 600px) {
  .button-container {
    flex-direction: column-reverse;
    align-items: center;
  }
}

.rounded-button:disabled {
  background-color: darkgrey;
}

.waiting {
  color: white;
  font-weight: 600;
  font-size: 3rem;
  background-color: rgba(134, 116, 201, 0.7);
  padding: 1rem;
  border-radius: 0.5rem;
}

section {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
