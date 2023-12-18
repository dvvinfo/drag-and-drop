<template>
  <div id="app">
    <div
      class="grid-container"
      ref="gridContainer"
      @mousemove="moveCircle"
      @mouseup="endDrag"
      v-if="gridIsReady"
    >
      <div class="grid">
        <div v-for="(row, rowIndex) in grid" :key="rowIndex" class="grid-row">
          <div
            v-for="(cell, cellIndex) in row"
            :key="cellIndex"
            class="grid-cell"
            :style="{
              top: rowIndex * cellSize + 'px',
              left: cellIndex * cellSize + 'px',
              width: cellSize + 'px',
              height: cellSize + 'px',
            }"
          ></div>
        </div>
      </div>
      <div
        class="draggable-circle"
        v-if="isDragging"
        :style="{
          top: circlePosition.y * cellSize + 'px',
          left: circlePosition.x * cellSize + 'px',
        }"
        @mousedown="startDrag"
      ></div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { Component } from "vue-property-decorator";

@Component
export default class App extends Vue {
  grid: number[][] = [
    [0, 0, 0, 0],
    [0, 0, 0, 0],
    [0, 0, 0, 0],
    [0, 0, 0, 0],
  ];
  cellSize = 100;
  isDragging = true;
  circlePosition: { x: number; y: number } = { x: 0.5, y: 0.5 };
  gridIsReady = false;

  mounted() {
    this.gridIsReady = true;
  }

  startDrag(event: MouseEvent) {
    this.moveCircle(event);
    document.addEventListener("mousemove", this.moveCircle);
  }

  moveCircle(event: MouseEvent) {
    if (this.isDragging) {
      const gridRect = (
        this.$refs.gridContainer as HTMLElement
      ).getBoundingClientRect();
      const x = event.clientX - gridRect.left;
      const y = event.clientY - gridRect.top;

      const col = Math.floor(x / this.cellSize);
      const row = Math.floor(y / this.cellSize);

      this.circlePosition.x = col + 0.5;
      this.circlePosition.y = row + 0.5;
    }
  }

  endDrag() {
    document.removeEventListener("mousemove", this.moveCircle);
  }
}
</script>

<style scoped lang="scss">
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #01224d;
}

.grid-container {
  position: relative;
  width: 400px;
  height: 400px;
  border: 2px solid #397bab;
  border-radius: 8px;
}

.grid {
  position: absolute;
  width: 100%;
  height: 100%;
}

.grid-row {
  display: flex;
}

.grid-cell {
  box-sizing: border-box;
  border: 2px solid #397bab;
}

.draggable-circle {
  position: absolute;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: #10a4d2;
  cursor: pointer;
  transform: translate(-50%, -50%);
  transition: all 0.2s ease-in-out;
}
</style>
