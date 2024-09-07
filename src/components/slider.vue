<template>
  <input
    type="range"
    :min="0"
    :max="max"
    :value="value"
    @input="updateValue"
    :disabled="disabled"
  />
</template>

<script>
export default {
  name: 'Slider',
  props: {
    max: {
      type: Number,
      default: 10
    },
    value: {
      type: Number,
      default: 0
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  mounted() {
    if (isNaN(parseInt(this.value))) {
      this.$emit('value-updated', this.max / 2)
    }
  },
  methods: {
    updateValue(event) {
      if (!this.disabled) {
        this.$emit('value-updated', Number(event.target.value))
      }
    }
  }
}
</script>

<style>
input[type='range'] {
  -webkit-appearance: none;
  appearance: none;
  background-color: transparent;
  width: 100%;
}

input[type='range']::-webkit-slider-runnable-track {
  -webkit-appearance: none;
  appearance: none;
  height: 8px;
  background: linear-gradient(to right, white 0%, #84369c 100%);
}

input[type='range']::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  height: 5em;
  width: 5em;
  background: url('@/images/cutestar3.png') center no-repeat;
  background-size: cover;
  cursor: grab;
  position: relative;
  top: 50%;
  transform: translateY(-50%);
}

.slider-wrapper {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 80vw;
  max-width: 100vw;
  padding: 0 1rem;
  padding-bottom: 2em;
  margin-bottom: 2em;
}

.slider-wrapper p {
  margin: 0;
  color: white;
  font-weight: 500;
  font-size: 3.5em;
}

.clueScalePhrase1 {
  flex: 1;
  justify-self: flex-start;
}

.clueScalePhrase2 {
  flex: 1;
  justify-self: flex-end;
}

@media (max-width: 430px) {
  input[type='range']::-webkit-slider-thumb {
    height: 8vw;
    width: 8vw;
  }
  .slider-wrapper p {
    font-size: 2em;
  }
}
</style>
