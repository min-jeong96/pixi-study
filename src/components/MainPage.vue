<template>
  <div>
    <img
      v-for="resource of resources"
      :key="resource"
      :src="require(`@/assets/${resource}`)"
      @click="addSprite(resource)"
    />
  </div>
  <div id="canvas-container">
    <div class>{{ canvasZoomScaleDisplay }}</div>
  </div>
</template>

<script>
import * as PIXI from 'pixi.js';
import SpritesheetData from '@/assets/sample.json';

/* Pixi.js dragging event 처리를 위한 전역 변수 */
var app = undefined;
var spritesheet = undefined;
var dragTarget = null;

/**
 * Pixi sprite drage-start event
 */
function onDragStart(event) {
  // 드래그 시
  this.alpha = 0.5;
  dragTarget = this;

  app.stage.on('pointermove', onDragMove);
}

/**
 * Pixi sprite drage-move event
 */
function onDragMove(event) {
  if (dragTarget) {
    dragTarget.anchor.set(0.5);
    dragTarget.parent.toLocal(event.global, null, dragTarget.position);
  }
}

/**
 * Pixi sprite drage-end event
 */
function onDragEnd() {
  if (dragTarget) {
    app.stage.off('pointermove');
    dragTarget.alpha = 1;

    // 드래그 종료 후 초기화
    dragTarget.alpha = 1;
    dragTarget.anchor.set(0);

    // 58px 정사각형 그리드에 놓이도록
    dragTarget.x = 58 * Math.floor(dragTarget.x / 58);
    dragTarget.y = 58 * Math.floor(dragTarget.y / 58);

    dragTarget = null;
  }
}
export default {
  name: 'MainPage',
  props: {},
  data() {
    return {
      canvasZoomScale: null,
      resources: [
        '11000000001.png',
        '11000000003.png',
        '11000000004.png',
        '11000000005.png',
        '11000000006.png',
        '11000010027.png',
      ],
    };
  },
  computed: {
    canvasZoomScaleDisplay: function () {
      return this.canvasZoomScale ? `${100 * this.canvasZoomScale}%` : 'Loading...';
    },
  },
  methods: {
    addSprite(resource) {
      if (!app || !spritesheet) return;

      const sprite = new PIXI.Sprite(spritesheet.textures[resource]);

      // sprite.anchor.set(0.5);
      sprite.scale.set(1);
      sprite.x = 0;
      sprite.y = 0;

      sprite.interactive = true;
      sprite.cursor = 'pointer';
      sprite.on('pointerdown', onDragStart, sprite);

      app.stage.addChild(sprite);
    },
  },
  setup() {
    // Create the SpriteSheet from data and image
    spritesheet = new PIXI.Spritesheet(
      PIXI.BaseTexture.from(require(`@/assets/${SpritesheetData.meta.image}`)),
      SpritesheetData
    );
    // Generate all the Textures asynchronously
    spritesheet.parse().then(() => {
      console.log('--- spritesheet parsed ---');
    });
  },
  async mounted() {
    /**
     * https://pixijs.download/dev/docs/PIXI.Spritesheet.html
     * https://pixijs.io/guides/basics/sprite-sheets.html
     */

    // Pixi initialize
    app = new PIXI.Application({ background: '#eeeeee', width: 2000, height: 2000 });
    document.getElementById('canvas-container').appendChild(app.view);

    app.stage.interactive = true;
    app.stage.hitArea = app.screen;
    app.stage.cursor = 'grab';
    app.stage.on('pointerup', onDragEnd);
    app.stage.on('pointerupoutside', onDragEnd);

    const container = document.getElementById('canvas-container');
    this.canvasZoomScale = 1; // zoom 값 초기화

    const border = new PIXI.Graphics();
    border.lineStyle(2, 0x000000, 0.2);
    border.drawRect(0, 0, 20 * 58, 20 * 58);
    app.stage.addChild(border);

    // zoom 기능 구현
    container.onwheel = (event) => {
      event.preventDefault();
      let addend = event.deltaY < 0 ? +0.25 : -0.25;
      const scale = app.stage.scale._x + addend;

      if (scale > 0) {
        app.stage.scale.set(scale);
        this.canvasZoomScale = scale;
      }
    };

    // panning 기능 구현
    let isStagePanning = false; // 현재 panning 여부
    let stagePanningMousePosition = { x: null, y: null }; // panning을 위한 mouse position 값 담는 변수

    // ondrag 쓰지 않은 이유 -> 실제 드래그 가능한 객체로 설정하지 않으면 이벤트 발생하지 않음
    container.onmousedown = (event) => {
      isStagePanning = true;
      stagePanningMousePosition = { x: event.offsetX, y: event.offsetY };
    };
    container.onmousemove = (event) => {
      if (isStagePanning && !dragTarget) {
        app.stage.x = app.stage.x + event.offsetX - stagePanningMousePosition.x;
        app.stage.y = app.stage.y + event.offsetY - stagePanningMousePosition.y;
        stagePanningMousePosition = { x: event.offsetX, y: event.offsetY };
      }
    };
    container.onmouseup = () => {
      isStagePanning = false;
      stagePanningMousePosition = { x: null, y: null };
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#canvas-container {
  /* width: fit-content; */
  width: 100%;
  height: 100%;
}
</style>
