<template>
  <div class="purpleBackground">
    <div class="pageContainer">
      <modal
        :show="showModal"
        :header="modalTitle"
        :blocks="modalBlocks"
        :modalHeading="modalHeading"
        @close="toggleModal"
      />
      <div class="back">
        <button
          class="rounded-button floating-button2"
          @click="logOut"
          aria-label="Log out"
        >
          Log Out
        </button>
      </div>
      <div class="logo">
        <img src="@/images/insyncLogo.png" alt="logo" />
      </div>
      <p class="welcome">Hi, {{ $userStore.name }}!</p>
      <div class="button-container">
        <button
          class="rounded-button floating-button1"
          @click="togglePlay"
          aria-label="Start playing"
        >
          PLAY
        </button>
        <button
          class="rounded-button floating-button2"
          @click="toggleModal"
          aria-label="Open instructions on how to play"
        >
          HOW TO PLAY
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import modal from '@/components/modal.vue'
</script>

<script>
export default {
  data() {
    return {
      showModal: false,
      modalHeading: 'How to Play',
      modalTitle: 'Team up with your <br> crew to spin the dial and hit <br> the hidden bullseye!',
      modalBlocks: [
        {
          title: 'The Catch',
          body: 'The target is sneaky—it’s hiding in a new spot each round, and you’ll have to guess where it is.'
        },
        {
          title: 'The Perk',
          body: 'One of your teammates has a special power—they know exactly where the target is! But there’s a twist: they can only drop a hint using a scale between two opposite ideas. Imagine a spectrum from “Sweet to Sour.” If the target is leaning towards “Sweet,” the clue might be something like “honey”—sweet, but not sugar-overload sweet.'
        },
        {
          title: 'Clue Time',
          body: 'Every round, a new teammate gives the clue, and the rest of you get to chat, debate, and strategize while the clue giver stays as cool as a cucumber.'
        },
        {
          title: 'The Reveal',
          body: 'When you’re all set, everyone taps “Ready,” and the big reveal happens—where’s the target?'
        },
        {
          title: 'Scoring',
          body: 'The closer you are to the center, the more points you rack up!'
        },
        {
          title: 'Teamwork',
          body: 'And remember, it’s all about teamwork—either you all win together, or you face the challenge again. Let the fun begin!'
        }
      ]
    }
  },
  created() {
    this.clearStores()
  },
  methods: {
    togglePlay() {
      this.$router.push('/play')
    },
    logOut() {
      this.$userStore.clear()
      this.clearStores()
      this.$success('You are logged out!')
      this.$router.push('/login')
    },
    toggleModal() {
      this.showModal = !this.showModal
    },
    clearStores() {
      this.$gameStore.clear()
      this.$clueStore.clear()
    }
  }
}
</script>

<style scoped>
.button-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.2rem;
}

p {
  margin: 1rem 2rem;
}

@keyframes float-middle {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(6px);
  }
}

@keyframes float-outer {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(3px);
  }
}

.floating-button1 {
  animation: float-outer 6s infinite ease-in-out;
}

.floating-button2 {
  animation: float-middle 6s infinite ease-in-out;
}
</style>
