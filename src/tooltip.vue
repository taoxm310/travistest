<template>
  <div class="m-tooltip" ref="tooltip">
    <div
      class="content-wrapper"
      v-if="visible"
      ref="contentWrapper"
      :class="{[`position-${position}`]:true}"
    >
      <slot name="content" :close="close"></slot>
    </div>
    <span ref="triggerWrapper" style="display:inline-block">
      <slot></slot>
    </span>
  </div>
</template>
<script>
export default {
  name: 'MTooltip',
  props: {
    position: {
      type: String,
      default: 'top',
      validator(value) {
        return ['top', 'right', 'left', 'bottom'].indexOf(value) !== -1
      }
    },
    trigger: {
      type: String,
      default: 'click',
      validator(value) {
        return ['click', 'hover'].indexOf(value) !== -1
      }
    }
  },
  data() {
    return {
      visible: false
    }
  },
  computed: {
    openEvent() {
      if (this.trigger === 'click') {
        return 'click'
      } else {
        return 'mouseenter'
      }
    },
    closeEvent() {
      if (this.trigger === 'click'){
        return 'click'
      } else {
        return 'mouseleave'
      }
    }
  },
  methods: {
    placeContent() {
      const { contentWrapper, triggerWrapper } = this.$refs
      document.body.appendChild(contentWrapper)
      let {
        width,
        top,
        left,
        height
      } = this.$refs.triggerWrapper.getBoundingClientRect()
      const { height: height2 } = contentWrapper.getBoundingClientRect()
      let positions = {
        top: { top: top + window.scrollY, left: left + window.scrollX },
        bottom: {
          top: top + height2 + window.scrollY,
          left: left + window.scrollX
        },
        left: {
          top: top + window.scrollY + (height - height2) / 2,
          left: left + window.scrollX
        },
        right: {
          top: top + window.scrollY + (height - height2) / 2,
          left: left + window.scrollX + width
        }
      }
      contentWrapper.style.left = positions[this.position].left + 'px'
      contentWrapper.style.top = positions[this.position].top + 'px'
    },
    open() {
      ;(this.visible = true),
        this.$nextTick(() => {
          this.placeContent()
          document.addEventListener('click', this.onClickDocument)
        })
    },
    close() {
      ;(this.visible = false),
        document.removeEventListener('click', this.onClickDocument)
    },
    onClickDocument(e) {
      if (
        this.$refs.tooltip &&
        (this.$refs.tooltip === e.target ||
          this.$refs.tooltip.contains(e.target))
      ) {
        return
      }
      if (
        this.$refs.contentWrapper &&
        (this.$refs.contentWrapper === e.target ||
          this.$refs.contentWrapper.contains(e.target))
      ) {
        return
      }
      this.close()
    },
    onClick(event) {
      if (this.$refs.triggerWrapper.contains(event.target)) {
        if (this.visible === true) {
          this.close()
        } else {
          this.open()
        }
      }
    }
  },
  mounted() {
    if (this.trigger === 'click') {
      this.$refs.tooltip.addEventListener('click', this.onClick)
    } else {
      this.$refs.tooltip.addEventListener('mouseenter', this.open)
      this.$refs.tooltip.addEventListener('mouseleave', this.close)
    }
  },
  destroyed() {
    if (this.trigger === 'click') {
      this.$refs.tooltip.removeEventListener('click', this.onClick)
    } else {
      this.$refs.tooltip.removeEventListener('mouseenter', this.open)
      this.$refs.tooltip.removeEventListener('mouseleave', this.close)
    }
  }
}
</script>
<style lang="scss" scoped>
$border-color: #333;
$border-radius: 4px;
.m-tooltip {
  display: inline-block;
  vertical-align: top;
  position: relative;
}
.content-wrapper {
  position: absolute;
  border-radius: 3px;
  border: 1px solid $border-color;
  border-radius: $border-radius;
  filter: drop-shadow(0 1px 1px rgba(0, 0, 0, 0.5));
  background-color: white;
  padding: 0.5em 1em;
  max-width: 20em;
  word-break: break-all;
  &:after,
  &::before {
    content: '';
    display: block;
    border: 10px solid transparent;
    width: 0;
    height: 0;
    position: absolute;
  }

  &.position-top {
    transform: translateY(-100%);
    margin-top: -10px;
    &::before,
    &::after {
      left: 10px;
      border-bottom: none;
    }
    &::before {
      border-top-color: black;
      top: 100%;
    }

    &:after {
      border-top-color: white;
      top: calc(100% - 1px);
    }
  }
  &.position-bottom {
    margin-top: 10px;
    &::before,
    &::after {
      left: 10px;
      border-top: none;
    }
    &::before {
      border-bottom-color: black;
      bottom: 100%;
    }

    &:after {
      border-bottom-color: white;
      bottom: calc(100% - 1px);
    }
  }
  &.position-left {
    transform: translateX(-100%);
    margin-left: -10px;
    &::before,
    &::after {
      transform: translateY(-50%);
      top: 50%;
      border-right: none;
    }
    &:before {
      border-left-color: black;
      left: 100%;
    }
    &:after {
      border-left-color: white;
      left: calc(100% - 1px);
    }
  }
  &.position-right {
    margin-left: 10px;
    &::before,
    &::after {
      transform: translateY(-50%);
      top: 50%;
      border-left: none;
    }
    &:before {
      border-right-color: black;
      right: 100%;
    }
    &:after {
      border-right-color: white;
      right: calc(100% - 1px);
    }
  }
}
</style>

