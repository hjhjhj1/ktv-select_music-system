<template>
  <div class="tab-bar-container" :style="containerStyle">
    <div class="tab-bar">
      <div
        v-for="(tab, index) in tabs"
        :key="index"
        class="tab-item"
        :class="{ 'active': currentIndex === index }"
        @click="handleTabClick(index)"
      >
        {{ tab }}
      </div>
      <div
        class="tab-indicator"
        :style="{
          transform: `translateX(${currentIndex * 100}%)`,
          transition: 'transform 200ms ease'
        }"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TabBar',
  props: {
    tabs: {
      type: Array,
      required: true,
      validator: (value) => value.every(item => typeof item === 'string')
    },
    onChange: {
      type: Function,
      required: true
    },
    initialIndex: {
      type: Number,
      default: 0
    },
    containerStyle: {
      type: Object,
      default: () => {}
    }
  },
  data() {
    return {
      currentIndex: this.initialIndex
    };
  },
  methods: {
    handleTabClick(index) {
      if (index !== this.currentIndex) {
        this.currentIndex = index;
        this.onChange(index);
      }
    }
  }
};
</script>

<style lang="less" scoped>
.tab-bar-container {
  height: 44px;
  overflow-x: auto;
  overflow-y: hidden;
  white-space: nowrap;
  position: relative;
}

.tab-bar {
  display: flex;
  height: 100%;
  position: relative;
}

.tab-item {
  flex: 1;
  min-width: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  cursor: pointer;
  font-size: 14px;
  color: #999;
  transition: color 200ms ease;

  &.active {
    font-weight: bold;
    color: #1890ff;
  }
}

.tab-indicator {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 2px;
  background-color: #1890ff;
  transform-origin: left center;
}
</style>