<template>
  <button 
    :class="{
      'default' : buttonType === 'default',
    }"
    @click="$emit('click')"
    >
    <slot />
   </button>
</template>

<script>
export default {
  name: "UiButton",
  
  props: {
    buttonType: {
      type: String,
      required: false,
      validator: (value) => ["default"].includes(value),
    },
    simonType: {
      type: String,
      required: false,
      validator: (value) => ["blue", "red", "yellow", "green"].includes(value),
    },
  },
};
</script>

<style scoped>

button {
    border: none;
}

button:hover {
  cursor: pointer;
}

.default {
  width: 100%;
  height: 40px;
}

</style>