<template>
  <div class="m-tabs-pane" :class="classes" v-if="active">
    <slot></slot>
  </div>
</template>
<script>
export default {
  name: 'MTabsPane',
  inject: ['eventBus'],
  props: {
    name: {
      type: String | Number,
      required: true
    }
  },
  data() {
    return {
      active: false
    }
  },
  computed: {
    classes() {
      return {
        active: this.active
      }
    }
  },
  created() {
    this.eventBus.$on('update:selected', name => {
      this.active = name === this.name
    })
  }
}
</script>
<style lang="scss" scoped>
.m-tabs-pane {
  padding: 1em;
  &.active {
  }
}
</style>

