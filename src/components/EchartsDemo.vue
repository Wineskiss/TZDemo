<!--  -->
<template>
  <div style="padding:10px;">
    <div :id="name" :style="'width:100%;height:' + (h - 10) + 'px;'"></div>
  </div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';
import "../../static/macarons";
export default {
  //import引入的组件需要注入到对象中才能使用
  components: {},
  data() {
    //这里存放数据
    return {
      myChart: {}
    };
  },
  props: {
    name: "",
    w: 0,
    h: 0,
    option: {},
    da: ""
  },
  //监控props中的数据变化
  watch: {
    //props中的da数据变化时触发
    da: {
      handler: function(val, oldval) {
        if (val.split("-")[0] == "A") {
          //判断父级传来的监听字段的内容 =A 为容器的子组件改变大小位置时触发只需要重新渲染图表
          this.myChart.resize();
        } else {
          //=B 为抽屉里的子组件改变配置时触发需要重新生成图表。并重新渲染图表（解决初始化图表宽度问题）
          this.myChart.clear(); //因为用的实的公用的变量，生成前需要删除之前的图表
          this.initecharts(); //生成图表
          this.myChart.resize(); //重新渲染图表
        }
      },
      immediate: false, //组件创建时是否触发此函数
      deep: true //是否深度监听
    }
  },
  methods: {
    //动态加载Echarts
    initecharts() {
      this.myChart = this.$echarts.init(
        document.getElementById(this.name),
        "macarons"
      );
      // 指定图表的配置项和数据
      // 使用刚指定的配置项和数据显示图表。
      this.myChart.setOption(this.option);
      window.addEventListener("resize", () => {
        this.myChart.resize();
      });
      this.myChart.resize(); //重新渲染图表
    }
  },
  //生命周期 - 创建完成（可以访问当前this实例）
  created() {},
  //生命周期 - 挂载完成（可以访问DOM元素）
  mounted() {
    this.initecharts();
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
<style></style>
