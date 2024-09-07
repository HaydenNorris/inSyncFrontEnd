<template>
  <div class="container" v-if="show">
    <div class="content">
      <slot>
        <div class="dizzy">
          <img width=300 src="@/images/dizzy.gif" alt="dizzy loading animation"/>
        </div>
        <div class="koba">
          <img width=700 src="@/images/kobaGif.gif" alt="dog loading animation"/>
        </div>
      </slot>
    </div>
  </div>
</template>

<script>
  export default { 
    props: {
      show: {
        type: Boolean,
        default: false
      }
    }, 
  }
</script>

<style scoped>
.container {
  height: 100vh; 
  width: 100vw;
  z-index: 100;
  background-color: rgba(94, 94, 94, 0.5);
  position: absolute; 
  display: flex;
  align-items: center;
}

.content {
  background: transparent;
  padding: 20px;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
}

.koba {
  margin-top: -7em;
}

</style>