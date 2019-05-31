<template>
  <div class="photo-fix"
    v-loading="loading"
    element-loading-text="照片修复中"
    element-loading-spinner="el-icon-loading"
    element-loading-background="rgba(0, 0, 0, 0.8)"
  >
    <div class="photo-containers">
      <div class="photo-container left-photo-container">
        <div
          class="small-photo-container"
          ref="smallContainer"
          @mousemove="handleMouseMove"
        >
          <div class="magnifier" :style="magnifierStyle" ref="magnifier" v-show="showMagnifier"></div>
          <img v-if="artworkSrc != ''" class="small-photo"  :src="artworkSrc" alt="原图">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </div>
        <div class="big-photo-container">
          <img
            v-if="showMagnifier"
            class="big-photo"
            ref="bigPhoto"
            :style="bigPhotoStyle"
            :src="artworkSrc"
            alt="放大后的图"
          >
          <div v-else class="tip-text">未上传照片</div>
        </div>
      </div>
      <div class="photo-container right-photo-container">
        <div class="small-photo-container">
          <img v-if="artworkSrc != ''" class="small-photo" :src="fixedArtworkSrc" alt="原图">
        </div>
        <div class="big-photo-container">
          <img
            v-if="showMagnifier"
            class="big-photo"
            ref="bigPhoto"
            :style="bigPhotoStyle"
            :src="fixedArtworkSrc"
            alt="放大后的图"
          >
          <div v-else class="tip-text">未上传照片</div>
        </div>
      </div>
    </div>
    <el-upload
      action="http://localhost:3000"
      class="upload"
      :on-progress="handleProgress"
      :on-success="handleSuccess"
      :show-file-list="false"
    >
      <el-button size="small" type="primary" class="button">上传照片并修复</el-button>
    </el-upload>
    <el-radio-group v-model="radio" @change="change" class="functions">
        <el-radio :label="1">超分辨率重建</el-radio>
        <el-radio :label="2">去噪</el-radio>
        <el-radio :label="3">修补</el-radio>
    </el-radio-group>
  </div>
</template>

<script>
import { constants } from 'crypto';
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
      radio: 1,
      function: 1,
      loading: false,
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
    handleMouseMove: function(event) {
      if(this.showMagnifier==true){
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
      }
    },
    handleSuccess(response, file, filelist){
      console.log("Success");
      let fs=file.raw;
      let burl=URL.createObjectURL(fs);
      this.artworkSrc=burl;
      let fname = fs.name;
      fname = fname.split(".")[0];
      let prefix = "../../uploads/photo-fix/";
      let postfix = "";
      let fun;
      switch(this.function){
        case 1:
          fun = "sr";
          break;
        case 2:
          fun = "denoising";
          break;
        case 3:
          fun = "inpainting";
          break;
      }
      prefix += fun + "/";
      postfix = "-" + fun + ".jpg";
      let result_name = prefix + fname + postfix;
      this.fixedArtworkSrc = result_name;
      this.showMagnifier = true;
      this.loading = false;
    },
    change(value){
      this.function = value;
    },
    handleProgress(event, file, fileList){
      console.log("Progressing");
      this.loading = true;
    },
  },
  
  mounted() {}
};
</script>

<style lang="scss">
.photo-fix {
  position: relative;
  width: fit-content;
  height: 600px;
  margin: 30px auto;
  padding: 0 150px;
  padding-top: 30px;
  border: 1px solid #DCDFE6;
  -webkit-box-shadow: 0 2px 4px 0 rgba(0,0,0,.12), 0 0 6px 0 rgba(0,0,0,.04);
  box-shadow: 0 2px 4px 0 rgba(0,0,0,.12), 0 0 6px 0 rgba(0,0,0,.04);

  .photo-containers {
    display: flex;
    justify-content: space-between;
    width: 600px;

    .left-photo-container {
      .big-photo-container {
        float: right;
        overflow: hidden;
      }
    }

    .right-photo-container {
      .big-photo-container {
        float: left;
        overflow: hidden;
      }
    }

    .photo-container {
      position: relative;
      width: 256px;

      .small-photo-container {
        position: relative;
        width: 256px;
        height: 256px;
        border: 1px dashed gray;
        box-sizing: border-box;

        .magnifier {
          position: absolute;
          width: 100px;
          height: 100px;
          background-color: #fff;
          opacity: 0.2;
          cursor: move;
        }
        .el-icon-plus {
          font-size: 50px;
          line-height: 258px;
        }
      }

      .big-photo-container {
        position: relative;
        width: 200px;
        height: 200px;
        margin-top: 10px;
        margin-right: 28px;
        margin-left: 28px;
        border: 1px solid gray;

        .big-photo {
          width: 512px;
          height: 512px;
        }
        .tip-text{
          text-align: center;
          width: 100%;
          height: 14px;
          font-size: 14px;
          position: absolute;
          top: 50%;
          transform: translate(0, -7px);
        }
      }
    }
  }

  .upload {
    position: absolute;
    bottom: 30px;
    left: 50%;
    transform: translateX(-50%);
  }
  .functions{
    display: block;
    margin-top: 30px;
  }
}
</style>
