<template>
  <div class="m-collapse-panel">
    <div class="title" @click="toggle" :data-name="name">{{title}}</div>
    <div class="content" v-if="open" ref="content">
      <slot></slot>
    </div>
  </div>
</template>
<script>
export default {
  name: 'MCollapse',
  inject: ['eventBus'],
  props: {
    title: {
      type: String,
      required: true
    },
    name: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      open: false
    }
  },
  mounted() {
    this.eventBus &&
      this.eventBus.$on('update:selected', names => {
        if (names.indexOf(this.name) === -1) {
          this.open = false
        } else {
          this.open = true
        }
      })
  },
  methods: {
    toggle() {
      if (this.open) {
        this.eventBus && this.eventBus.$emit('update:removeSelected', this.name)
      } else {
        this.eventBus && this.eventBus.$emit('update:addSelected', this.name)
      }
    }
  }
}
</script>
<style lang="scss" scoped>
$grey: #ddd;
$border-radius: 4px;
.m-collapse-panel {
  .title {
    border: 1px solid $grey;
    margin-top: -1px;
    margin-left: -1px;
    min-height: 32px;
    display: flex;
    align-items: center;
    padding: 0 8px;
    background-color: lighten($grey, 8%);
  }
  &:first-child {
    .title {
      border-top-left-radius: $border-radius;
      border-top-right-radius: $border-radius;
    }
  }
  &:last-child {
    .title {
      border-bottom-left-radius: $border-radius;
      border-bottom-right-radius: $border-radius;
    }
  }
  .content {
    padding: 8px;
  }
}
</style>
