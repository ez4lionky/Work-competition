<template>
  <div id="app">
    <div class="demo">
      <el-tabs type="border-card" v-model="activeName">
        <el-tab-pane label="风格转换" name="first" :key="'first'">
          <section>
            <span>Style migration with an image</span>
            <br>
            <br>
            <el-upload
              class="avatar-uploader"
              action="http://localhost:3000"
              :show-file-list="false"
              :on-change="handleArtworkChange"
              :on-progress="handleProgress"
              :on-success="handleSuccess">
              <img v-if="artworkSrc != ''" :src="artworkSrc" class="avatar">
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              <div v-if="artworkSrc == ''" class="el-upload__text">选择作品</div>
            </el-upload>
            <el-upload
              class="avatar-uploader"
              action="http://localhost:3000"
              :show-file-list="false"
              :on-change="handleStyleChange"
              :on-progress="handleProgress"
              :on-success="handleSuccess">
              <img v-if="styleSrc!= ''" :src="styleSrc" class="avatar">
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              <div v-if="artworkSrc == ''" class="el-upload__text">选择风格</div>
            </el-upload>
            <MediaDiff
              :origin="origin"
              :diff="diff"
              cursor="circle" 
              style="width: 256px; height: 256px"
              v-loading="loading"
              element-loading-text="照片转换中"
              element-loading-spinner="el-icon-loading"
              element-loading-background="rgba(0, 0, 0, 0.8)"
            >
              <div slot="tip" class="el-upload__tip">结果对比</div>
            </MediaDiff>
          </section>
          <section>
            <div class="style">
              <img :src="ex_art[0]">
            </div>
            <div class="style">
              <img :src="ex_style[0]">
            </div>
            <MediaDiff
              :origin="ex_art[0]"
              :diff="ex_result[0]"
              cursor="circle"
              style="width: 256px; height: 256px"
            ></MediaDiff>
          </section>
          <section>
            <div class="style">
              <img :src="ex_art[1]">
            </div>
            <div class="style">
              <img :src="ex_style[1]">
            </div>
            <MediaDiff
              :origin="ex_art[1]"
              :diff="ex_result[1]"
              cursor="circle"
              style="width: 256px; height: 256px"
            ></MediaDiff>
          </section>
          <section>
            <div class="style">
              <img :src="ex_art[2]">
            </div>
            <div class="style">
              <img :src="ex_style[2]">
            </div>
            <MediaDiff
              :origin="ex_art[2]"
              :diff="ex_result[2]"
              cursor="circle"
              style="width: 256px; height: 256px"
            ></MediaDiff>
          </section>
        </el-tab-pane>
        <el-tab-pane label="照片修复" name="second" :key="'second'">
          <PhotoFix/>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>

<script>
import MediaDiff from "./components/MediaDiff";
import PhotoFix from "./pages/PhotoFix";

export default {
  name: "app",
  data() {
    return {
      activeName: "first",
      artworkSrc: "",
      styleSrc: "",
      artwork: "",
      style: "",
      resultSrc: "",
      origin: "src/assets/images/bg.jpg",
      diff: "src/assets/images/bg.jpg",
      ex_art: ["uploads/style/artworks/artwork2.jpg", "uploads/style/artworks/artwork3.jpg", "uploads/style/artworks/artwork4.jpg"],
      ex_style: ["uploads/style/styles/style4.jpg", "uploads/style/styles/style5.jpg", "uploads/style/styles/style6.jpg", "uploads/style/styles/style7.jpg"],
      ex_result: ["src/assets/images/results/artwork2-style4.jpg", "src/assets/images/results/artwork3-style5.jpg", "src/assets/images/results/artwork4-style6.jpg", "src/assets/images/results/artwork5-style7.jpg"],
      loading: false,
    };
  },
  components: {
    MediaDiff,
    PhotoFix
  },
  methods: {
    handleArtworkChange(file){
      let fs=file.raw;
      let burl=URL.createObjectURL(fs);
      this.artworkSrc=burl;
      this.artwork = fs.name.split(".")[0];
      if(this.artwork!='' && this.style!=''){
        this.resultSrc = "src/assets/images/results/" + this.artwork + "-" + this.style + ".jpg"
        this.loading = false
      }
    },
    handleStyleChange(file){
      let fs=file.raw;
      let burl=URL.createObjectURL(fs);
      this.styleSrc=burl;
      this.style = fs.name.split(".")[0];
      if(this.artwork!='' && this.style!=''){
        this.resultSrc = "src/assets/images/results/" + this.artwork + "-" + this.style + ".jpg"
        this.loading = false
      }
    },
    handleProgress(event, file, fileList){
      if(this.artwork!='' && this.style!=''){
        this.loading=true
      }
    },
    handleSuccess(response, file, fileList){
      if(this.artwork!='' && this.style!=''){
        this.origin = this.artworkSrc
        this.diff = this.resultSrc
      }
    }

  }
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
}

#app {
  width: 100%;
  background-color: #fff;
  font-family: Tahoma, Helvetica, Arial, "Microsoft Yahei", "微软雅黑", STXihei,
    "华文细黑", sans-serif;
  a {
    color: #42b883;
  }
}

.intro {
  width: 100%;
  text-align: center;
  margin-top: 80px;
}

.demo {
  background-color: #f9f9f9;
  text-align: center;

  section {
    width: 100%;
    text-align: center;
    padding: 36px;
    box-sizing: border-box;
    span {
      font-size: 18px;
      color: #42b883;
    }
  }
}
  .el-upload {
    display: inline-block;
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
    .el-upload__text{
      position: absolute;
      bottom: 70px;
      left: 50%;
      transform: translateX(-50%);
    }
  }
  .el-upload:hover {
    border-color: #409EFF;    
  }
  .avatar-uploader{
    display: inline-block;
    vertical-align: middle;
    margin-right: 100px;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 256px;
    height: 256px;
    line-height: 256px;
    text-align: center;
  }
  .avatar {
    width: 256px;
    height: 256px;
    display: block;
  }
  .style {
    width: 256px;
    height: 256px;
    display: inline-block;
    vertical-align: middle;
    margin-right: 100px;
  
    img {
      width: 256px;
      height: 256px;
    }
}
</style>
