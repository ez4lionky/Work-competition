<template>
  <div id="app">
    <div class="demo">
      <el-tabs type="border-card" v-model="activeName">
        <el-tab-pane label="风格转换" name="first" :key="'first'">
          <section>
            <span>Style migration with an image</span>
            <br>
            <br>
            <div class="style">
              <img src="src/assets/images/style-migration/styles/style1.jpg">
            </div>
            <MediaDiff
              :origin="origin"
              :diff="diff"
              cursor="circle"
              style="width: 256px; height: 256px"
            ></MediaDiff>
          </section>
          <section>
            <span>Style migration with an image</span>
            <br>
            <br>
            <div class="style">
              <img src="src/assets/images/style-migration/styles/style1.jpg">
            </div>
            <MediaDiff
              :origin="origin"
              :diff="diff"
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
      origin: "src/assets/images/style-migration/artworks/artwork1.jpg",
      diff: "src/assets/images/style-migration/results/artwork1-style1.jpg",
      origin_video: "src/assets/diff.mp4",
      diff_video: "src/assets/origin.mp4"
    };
  },
  components: {
    MediaDiff,
    PhotoFix
  },
  methods: {
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePreview(file) {
      console.log(file);
    },
    handleSuccess(n, u) {
      fileList.append({ name: n, url: u });
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
