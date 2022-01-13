<!--  -->
<template>
  <div class="titlediv">
    <Row>
      <Col span="3">
        <span>
          <br />
        </span>
      </Col>
      <Col span="18">
        <div>
          <span class="title">Demo</span>
        </div>
      </Col>
      <Col span="3">
        <div class="title-div">
          <br />
          {{ datetime }}
        </div>
      </Col>
    </Row>
  </div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';

export default {
  //import引入的组件需要注入到对象中才能使用
  components: {},
  data() {
    //这里存放数据
    return {
      datetime: ""
    };
  },
  //监听属性 类似于data概念
  computed: {},
  //监控data中的数据变化
  watch: {},
  //方法集合
  methods: {
    Getdatetime() {
      var _this = this;
      let yy = new Date().getFullYear();
      let mm = new Date().getMonth() + 1;
      let dd = new Date().getDate();
      let hh = new Date().getHours();
      let mf =
        new Date().getMinutes() < 10
          ? "0" + new Date().getMinutes()
          : new Date().getMinutes();
      let ss =
        new Date().getSeconds() < 10
          ? "0" + new Date().getSeconds()
          : new Date().getSeconds();
      _this.datetime =
        yy + "年" + mm + "月" + dd + "日 " + hh + ":" + mf + ":" + ss;
    }
  },
  //生命周期 - 创建完成（可以访问当前this实例）
  created() {
    //clearInterval(setInterval(this.Getdatetime, 1000));
  },
  //生命周期 - 挂载完成（可以访问DOM元素）
  mounted() {
    var interval = setInterval(this.Getdatetime, 1000);
  },
  beforeCreate() {}, //生命周期 - 创建之前
  beforeMount() {}, //生命周期 - 挂载之前
  beforeUpdate() {}, //生命周期 - 更新之前
  updated() {}, //生命周期 - 更新之后
  beforeDestroy() {}, //生命周期 - 销毁之前
  destroyed() {}, //生命周期 - 销毁完成
  activated() {} //如果页面有keep-alive缓存功能，这个函数会触发
};
</script>
<style scoped>
.titlediv {
  /* background-color: rgba(16, 37, 87); */
  /* border-top: solid 10px rgba(31, 247, 254); */
  border-radius: 5px;
  /* border-left: solid 500px rgba(21,16,80,0);
    border-right: solid 500px rgba(21,16,80,0);
    border-bottom: solid 3px rgba(31, 247, 254); */
  box-shadow: 0px 15px 10px -10px rgba(31, 247, 254);
  /* box-shadow: 0 0px 3px 3px rgba(31, 247, 254); */
}
.title {
  font-size: 30px;
  font-weight: 900;
  line-height: 45px;
  margin: 0px 1%;
}
.title-div {
  text-align: right;
  float: left;
}
</style>
