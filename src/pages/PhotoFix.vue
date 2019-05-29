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
          <img class="small-photo"  :src="artworkSrc" alt="原图">
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
      action="http://localhost:3000"
      class="upload"
      :before-upload="beforeUpload"
      :on-change="handlePreview"
      :on-success="handleSuccess"
      :show-file-list="false"
    >
      <el-button size="small" type="primary">点击上传</el-button>
    </el-upload>
  </div>
</template>

<script>
let artwork1 = "";
let fixedArtwork1="";
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
  watch:{
    files(newval,oldval){
      
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
      return true;
    },
    handlePreview(file){
      let fs=file.raw;
      let burl=URL.createObjectURL(fs);
      artwork1=burl;
      let fname = fs.name;
      fname = fname.split(".")[0];
      let result_name = "../../uploads/" + fname + "-fixed.jpg";
      console.log(result_name);
      this.fixedArtworkSrc = result_name;
      // let reader=new FileReader();
      // reader.onload=function(e){
      //   let c=e.target.result;
      //   alert(c);
      // }
      // reader.readAsText(fs,"UTF-8");
    },
    handleSuccess(res,file,fileList){
      
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
      position: relative;
      width: 256px;
      overflow: hidden;

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
    bottom: 10px;
    left: 50%;
    transform: translateX(-50%);
  }
}
</style>
