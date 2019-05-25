<template>
  <div class="photo-fix">
    <div class="photo-containers">
      <div class="photo-container left-photo-container">
        <div
          class="small-photo-container"
          ref="smallContainer"
          @mouseenter="handleMouseEnter"
          @mouseleave="handleMouseLeave"
          @mousemove="handleMouseMove"
        >
          <div class="magnifier" :style="magnifierStyle" ref="magnifier" v-show="showMagnifier"></div>
          <img class="small-photo" :src="artworkSrc" alt="原图">
        </div>
        <div class="big-photo-container" v-show="showMagnifier">
          <img
            class="big-photo"
            ref="bigPhoto"
            :style="bigPhotoStyle"
            :src="artworkSrc"
            alt="放大后的图"
          >
        </div>
      </div>
      <div class="photo-container right-photo-container">
        <div class="small-photo-container">
          <img class="small-photo" :src="fixedArtworkSrc" alt="原图">
        </div>
        <div class="big-photo-container" v-show="showMagnifier">
          <img
            class="big-photo"
            ref="bigPhoto"
            :style="bigPhotoStyle"
            :src="fixedArtworkSrc"
            alt="放大后的图"
          >
        </div>
      </div>
    </div>
    <el-upload
      action="#"
      class="upload"
      :before-upload="beforeUpload"
      :file-list="fileList"
    >
      <el-button size="small" type="primary">点击上传</el-button>
      <div slot="tip" class="el-upload__tip">只能上传jpg/png文件</div>
    </el-upload>
  </div>
</template>

<script>
const artwork1 = require('../assets/images/photo-fix/artworks/artwork1.jpg');
const fixedArtwork1 = require('../assets/images/photo-fix/fixed/artwork1-fixed.jpg');
export default {
  data: function() {
    return {
      showMagnifier: false,
      magnifierX: 0,
      magnifierY: 0,
      marginLeft: 0,
      marginTop: 0,
      artworkSrc: "",
      fixedArtworkSrc: "",
      fileList: []
    };
  },
  computed: {
    magnifierStyle: function() {
      return { top: `${this.magnifierY}px`, left: `${this.magnifierX}px` };
    },
    bigPhotoStyle: function() {
      return {
        marginLeft: `-${this.marginLeft}px`,
        marginTop: `-${this.marginTop}px`
      };
    }
  },
  methods: {
    handleMouseEnter: function() {
      this.showMagnifier = true;
    },
    handleMouseLeave: function() {
      this.showMagnifier = false;
    },
    handleMouseMove: function(event) {
      let { smallContainer, magnifier, bigPhoto } = this.$refs;
      let smallContainerOffsetLeft = smallContainer.offsetLeft;
      let smallContainerOffsetTop = smallContainer.offsetTop;

      while (smallContainer.offsetParent.nodeName !== "BODY") {
        smallContainer = smallContainer.offsetParent;
        smallContainerOffsetLeft += smallContainer.offsetLeft;
        smallContainerOffsetTop += smallContainer.offsetTop;
      }
      smallContainer = this.$refs.smallContainer;

      const maxX = smallContainer.clientWidth - magnifier.clientWidth;
      const maxY = smallContainer.clientHeight - magnifier.clientHeight;

      let magnifierX = Math.min(
        maxX,
        event.pageX - smallContainerOffsetLeft - magnifier.clientWidth / 2
      );
      let magnifierY = Math.min(
        maxY,
        event.pageY - smallContainerOffsetTop - magnifier.clientHeight / 2
      );
      if (magnifierX < 0) magnifierX = 0;
      if (magnifierY < 0) magnifierY = 0;
      this.magnifierX = magnifierX;
      this.magnifierY = magnifierY;

      const scale = bigPhoto.clientWidth / smallContainer.clientWidth;
      this.marginLeft = magnifierX * scale;
      this.marginTop = magnifierY * scale;
    },
    beforeUpload: function({name}) {
      this.artworkSrc = artwork1;
      this.fixedArtworkSrc = fixedArtwork1;
      return false;
    }
  },
  mounted() {}
};
</script>

<style lang="scss">
.photo-fix {
  position: relative;
  width: fit-content;
  height: 560px;
  margin: 50px auto;
  padding: 0 200px;
  padding-top: 50px;
  border: 1px solid gray;

  .photo-containers {
    display: flex;
    justify-content: space-between;
    width: 600px;

    .left-photo-container {
      .big-photo-container {
        float: right;
      }
    }

    .right-photo-container {
      .big-photo-container {
        float: left;
      }
    }

    .photo-container {
      overflow: hidden;
      position: relative;
      width: 256px;

      .small-photo-container {
        position: relative;
        width: 256px;
        height: 256px;
        border: 1px solid gray;

        .magnifier {
          position: absolute;
          width: 100px;
          height: 100px;
          background-color: #fff;
          opacity: 0.2;
          cursor: move;
        }
      }

      .big-photo-container {
        width: 200px;
        height: 200px;
        margin-top: 10px;
        overflow: hidden;

        .big-photo {
          width: 512px;
          height: 512px;
        }
      }
    }
  }

  .upload {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    bottom: 10px;
  }
}
</style>
