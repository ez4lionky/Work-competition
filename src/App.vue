<template>
  <div id="app">
    <div class="demo">
      <el-tabs type="border-card" v-model="activeName">
        <el-tab-pane label="风格转换" name="first" :key="'first'">
          <section>
            <span>Style migration with an image</span>
            <br>
            <br>
            <div class='style'>
              <img src="src/assets/c-style1.jpg"> </img>
            </div>
            <MediaDiff :origin="origin"
                      :diff="diff"
                      cursor="circle"
                      style="width: 256px; height: 256px">
            </MediaDiff>
         </section>
         <section>
            <span>Style migration with an image</span>
            <br>
            <br>
            <div class='style'>
              <img src="src/assets/c-style1.jpg"> </img>
            </div>
            <MediaDiff :origin="origin"
                      :diff="diff"
                      cursor="circle"
                      style="width: 256px; height: 256px">
            </MediaDiff>
         </section>
        </el-tab-pane>
        <el-tab-pane label="照片修复" name="second" :key="'second'">
          <div style="background: green; display: inline">
              tab2内容
              tab2内容
              tab2内容
          </div>
        </el-tab-pane>
      </el-tabs>
  
    </div>
  </div>
</template>

<script>

  import MediaDiff from './Component.vue';

  export default {
    name: 'app',
    data() {
      return {
        activeName: "first",
        origin: "src/assets/content.jpg",
        diff: "src/assets/c-result1.jpg",
        origin_video: "src/assets/diff.mp4",
        diff_video: "src/assets/origin.mp4"
      }
    },
    components: {
      MediaDiff
    },
    methods: {
      handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePreview(file) {
        console.log(file);
      },
      handleSuccess(n, u){
        fileList.append({name: n, url: u})
      }
    }
  }
</script>

<style lang="scss">
  *{
    margin: 0;
    padding: 0;
  }
  #app{
    width: 100%;
    background-color: #fff;
    font-family: Tahoma, Helvetica, Arial, "Microsoft Yahei","微软雅黑", STXihei, "华文细黑", sans-serif;
    a{
      color: #42b883;
    }
  }

  .intro{
    width: 100%;
    text-align: center;
    margin-top: 80px;
  }

  .demo{
    background-color: #f9f9f9;
    text-align: center;

    section{
      width: 100%;
      text-align: center;
      padding: 36px;
      box-sizing: border-box;
      span{
        font-size: 18px;
        color: #42b883;
      }
    }
  }

  .style{
    width: 256px;
    height: 256px;
    display:inline-block;
    vertical-align: middle;
    margin-right: 100px;
    img{
      width: 256px;
      height: 256px;
    }
  }
</style>
