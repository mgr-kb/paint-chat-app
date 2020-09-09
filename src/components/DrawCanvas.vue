<template>
  <div class="DrawCanvas">
    <h1>{{ msg }}</h1>
    <div class="DrawCanvas__CanvasContainer">
      <canvas
        id="drawCanvas"
        :class="{eraser: state.canvasMode === 'eraser'}"
        width="640"
        height="480"
        @mousedown="dragStart"
        @mouseup="dragEnd"
        @mouseout="dragEnd"
        @mousemove="draw">
      </canvas>
    </div>
    <div class="DrawCanvas__ToolContainer">
      <button class="DrawCanvas__ClearBtn" @click="clearCanvas">
        クリア
      </button>
      <button @click="changeCanvasMode('eraser')">消しゴム</button>
      <button @click="changeCanvasMode('pen')">ペンモード</button>
      <select @change="changePenColor" v-model="state.selectedColor">
        <option v-for="color in state.colors" :value="color.code" :key="color.name">
          {{ color.name }}
        </option>
      </select>
      <span>{{ state.selectedColor }}</span>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, onMounted } from 'vue';

type CanvasMode = 'pen' | 'eraser';

// MouseEvent発生箇所の座標(2D)を取得する
const getCoordinate2D = (event: MouseEvent) => {
  const x = event.offsetX;
  const y = event.offsetY;
  return { x, y }
};

export default defineComponent({
  props: {
    msg: String,
  },
  setup() {
    const canvas = {} as HTMLCanvasElement;
    const context = {} as CanvasRenderingContext2D;
    const isDrag = false;
    const canvasMode = 'pen' as CanvasMode;
    const selectedColor = '#333';
    const colors = [
      { code: '#333', name: '黒色' },
      { code: '#ff0000', name: '赤色' },
      { code: '#0000ff', name: '青色' },
    ];
    const state = reactive({
      canvas,
      context,
      isDrag,
      canvasMode,
      selectedColor,
      colors,
    });
    // 描画
    const draw = (event: MouseEvent) => {
      const { x, y } = getCoordinate2D(event);
      if (!state.isDrag) {
        return;
      }
      state.context.lineTo(x, y);
      state.context.stroke();
    };
    // 描画開始
    const dragStart = (event: MouseEvent) => {
      const { x, y } = getCoordinate2D(event);
      state.context.beginPath();
      state.context.lineTo(x, y);
      state.context.stroke();
      state.isDrag = true;
    };
    // 描画終了
    const dragEnd = () => {
      state.context.closePath();
      state.isDrag = false;
    };
    // クリア
    const clearCanvas = () => {
      state.context.clearRect(0, 0, state.canvas.width, state.canvas.height);
    };
    // ペンの色変更
    const changePenColor = () => {
      alert(state.selectedColor);
      state.context.strokeStyle = state.selectedColor;
    };
    // canvasモードの変更
    const changeCanvasMode = (canvasMode: CanvasMode) => {
      // カーソル変更
      state.canvasMode = canvasMode;

      // 描画設定変更
      state.context.lineCap = canvasMode === 'pen' ? 'round' : 'square';
      state.context.lineJoin = canvasMode === 'pen' ? 'round' : 'miter';
      state.context.lineWidth = canvasMode === 'pen' ? 5 : 30;
      state.context.strokeStyle = canvasMode === 'pen' ? '#333' : '#fff';
    };
    // DOMがmountされたらcanvasを取得
    onMounted(() => {
      state.canvas = document.getElementById('drawCanvas') as HTMLCanvasElement;
      state.context = state.canvas.getContext('2d') as CanvasRenderingContext2D;
      if (context) {
        state.context.lineCap = 'round';
        state.context.lineJoin = 'round';
        state.context.lineWidth = 5;
        state.context.strokeStyle = '#333';
      }
    })
    return {
      state,
      draw,
      dragStart,
      dragEnd,
      clearCanvas,
      changePenColor,
      changeCanvasMode,
    }
  }
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
#drawCanvas {
  border: 1px solid #555;
}
.eraser {
  cursor: url("../assets/images/eraser.png") 15 15, auto;
}
</style>
