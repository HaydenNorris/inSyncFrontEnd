<template>
  <div class="container" v-if="show" @click="close">
    <div class="content">
      <div class="header">
        <div class="closeButton">
          <img src="@/images/close.png" alt="Close Button" @click="exit" />
        </div>
        <img src="@/images/howToIcon3.png" alt="How to Icon"/>
        <h2>{{ modalHeading }}</h2>
      </div>
      <p class="objective" v-html="header"></p>
      <div class="carousel">
        <transition name="fade" mode="out-in">
          <div class="whiteBox" :key="currentIndex">
            <p>
              <b>{{ blocks[currentIndex].title }}: </b>{{ blocks[currentIndex].body }}
            </p>
          </div>
        </transition>
        <div class="button-container">
          <button class="prev" @click="prevSlide"><</button>
          <button class="next" @click="nextSlide">></button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
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
      currentIndex: 0
    }
  },
  methods: {
    close(e) {
      if (e.target.className === 'container') {
        this.exit()
      }
    },
    exit() {
      this.$emit('close')
    },
    nextSlide() {
      if (this.currentIndex < this.blocks.length - 1) {
        this.currentIndex++
      } else {
        this.currentIndex = 0
      }
    },
    prevSlide() {
      if (this.currentIndex > 0) {
        this.currentIndex--
      } else {
        this.currentIndex = this.blocks.length - 1
      }
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
  font-size: 0.98rem;
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
  color: #382a5f;
}

.closeButton {
  position: absolute;
  top: 1.2em;
  right: 0.1em;
  margin: 0;
  background-color: transparent;
  border: transparent;
}

.closeButton img {
  width: 30px;
  height: 30px;
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
</style>
