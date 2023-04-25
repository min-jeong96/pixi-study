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
function onDragStart() {
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

    app = new PIXI.Application({ background: '#1099bb' });
    document.getElementById('canvas-container').appendChild(app.view);

    app.stage.interactive = true;
    app.stage.hitArea = app.screen;
    app.stage.on('pointerup', onDragEnd);
    app.stage.on('pointerupoutside', onDragEnd);

    const container = document.getElementById('canvas-container');
    this.canvasZoomScale = 1; // zoom 값 초기화

    // zoom 기능 구현
    container.onwheel = (event) => {
      let addend = event.deltaY < 0 ? -0.25 : 0.25;
      const scale = app.stage.scale._x + addend;

      if (scale > 0) {
        app.stage.scale.set(scale);
        this.canvasZoomScale = scale;
      }
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#canvas-container {
  width: fit-content;
}
</style>
