<template>
  <div class="m-cascader-item" :style="{height:height}">
    <div class="left">
      <div class="label" v-for="item in items" @click="leftSelected=item" :key="item.name">
        {{ item.name}}
        <icon class="icon" v-if="item.children" name="right"></icon>
      </div>
    </div>
    <div class="right" v-if="rightItems">
      <m-cascader-item :items="rightItems" :height="height">
      </m-cascader-item>
    </div>
  </div>
</template>

<script>
import Icon from './icon.vue'
export default {
  name: 'MCascaderItem',
  components:{
    Icon
  },
	props: {
		items: {
			type: Array
    },
    height:{
      type:String
    }
	},
	data() {
		return {
			leftSelected: null
		}
  },
  computed:{
    rightItems(){
      if (this.leftSelected && this.leftSelected.children){
        return this.leftSelected.children
      } else {
        return null
      }
    }
  }
}
</script>

<style lang="scss" scoped>
@import 'var';
.m-cascader-item {
  display: flex;
  align-items: flex-start;
  justify-content: flex-start;
  height: 100px;
  .left {
    height: 100%;
    padding: .3em 0;
  }
  .right {
    height: 100%;
    border-left: 1px solid $border-color-light;
  }
  .label {
    padding: .3em 1em;
    display: flex;
    align-items: center;
    .icon {
      margin-left: 1em;
      transform: scale(0.5)
    }
  }
}
</style>

