<script setup lang="ts">
import { ref, onMounted, onUnmounted, computed } from 'vue';
import { currentTicker } from '../state';

const currentIndex = ref(0);
let interval: number | undefined;

const pickNext = () => {
  currentIndex.value = (currentIndex.value + 1) % currentTicker.value.quotes.length;
};

const scrollDurationMs = computed(() => currentTicker.value.interval_ms);

onMounted(() => {
  interval = window.setInterval(pickNext, currentTicker.value.interval_ms);
});
onUnmounted(() => {
  if (interval !== undefined) clearInterval(interval);
});
</script>

<template>
  <div class="ticker">
    <div
      :key="currentIndex"
      class="ticker-inner"
      :style="{ animationDuration: scrollDurationMs + 'ms' }"
    >
      {{ currentTicker.quotes[currentIndex].text }}
    </div>
  </div>
</template>

<style scoped>
.ticker {
  margin: 0;
  padding: 6px 0;
  width: 100%;
  box-sizing: border-box;
  font-size: 11px;
  background: var(--theme-surface, #ebe2c4);
  color: var(--theme-muted, #6b5a3d);
  border-bottom: 1px solid var(--theme-border, #2a2218);
  overflow: hidden;
  font-family: var(--theme-font-masthead, -apple-system, sans-serif);
  letter-spacing: 0.5px;
  text-transform: uppercase;
}
.ticker-inner {
  display: inline-block;
  white-space: nowrap;
  padding-left: 100%;
  animation-name: ticker-marquee;
  animation-timing-function: linear;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
  will-change: transform;
}
@keyframes ticker-marquee {
  0%   { transform: translateX(0); }
  100% { transform: translateX(-100%); }
}
@media (prefers-reduced-motion: reduce) {
  .ticker-inner {
    animation: none;
    padding-left: 14px;
    padding-right: 14px;
    text-overflow: ellipsis;
    overflow: hidden;
    max-width: 100%;
  }
}
</style>
