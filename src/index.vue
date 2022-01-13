<template>
  <div>
    <div class="board" style="width: 100%">
      <!-- <div class="zhuan">
        <Button
          type="primary"
          shape="circle"
          icon="md-sync"
          @click="zhuanhuan"
        ></Button>
      </div> -->
      <Menu mode="horizontal" theme="dark" active-name="1">
        <MenuItem name="1">
          <span @click="initfang">添加模块</span>
        </MenuItem>
      </Menu>
      <div class="home">
        <grid-layout
          :layout="layoutData"
          :col-num="100"
          :row-height="layoutHeight"
          :is-draggable="dialogVisible"
          :is-resizable="dialogVisible"
          :is-mirrored="false"
          :vertical-compact="false"
          :margin="[10, 10]"
          :use-css-transforms="true"
          @layout-updated="layoutUpdatedEvent"
        >
          <!-- <Canvas></Canvas> -->
          <!-- <div id="container" style="height:100vh;width:100vw;"></div> -->
          <grid-item
            v-for="item in layoutData"
            :x="item.x"
            :y="item.y"
            :w="item.w"
            :h="item.h"
            :i="item.i"
            :key="item.i"
            @resized="resizedEvent"
            :style="item.style"
          >
            <!-- <Icon
              type="ios-cog-outline"
              size="20"
              :style="'color:#fff;position:absolute;top: 1px;right:1px;z-index:9'"
              @click="updateClick(item.i)"
            />-->
            <echartsdemo
              v-if="item.index == 0"
              :da="iid"
              :h="item.h * layoutHeight * 1.2 - 10"
              :option="item.option"
              :name="item.name"
            ></echartsdemo>
            <div :id="item.name" v-if="item.index == 1"></div>
          </grid-item>
        </grid-layout>
      </div>
    </div>
    <Drawer
      title="添加图表"
      placement="left"
      width="800"
      :closable="false"
      v-model="open1"
    >
      <Row>
        <i-col>
          <Input v-model="NameValue" placeholder="Name" style="width: 360px;" />
          <br />
          <Input
            v-model="EchartsValue"
            @on-change="Echartsyulan"
            placeholder="Echarts"
            style="width: 360px;margin-top:20px"
          />
          <br />
          <Button
            @click="initOK"
            type="primary"
            style="width: 360px;margin-top:20px"
            >创建</Button
          >
        </i-col>
      </Row>
      <Row style="margin-top:40px">
        <i-col>
          <H1>图表预览</H1>
          <br />
          <echartsdemo
            :da="yulan"
            :h="400"
            :option="yulanoption"
            name="yulandemo"
          ></echartsdemo>
        </i-col>
      </Row>
    </Drawer>
    <!-- <Modal
      width="80%"
      v-model="modal1"
      title="编辑"
      @on-ok="ok"
      @on-cancel="cancel"
    >
      <Row></Row>
    </Modal> -->
  </div>
</template>

<script>
import Vue from "vue";
import "../static/macarons.js";
import layoutData from "../static/layoutData.json";
import VueGridLayout from "vue-grid-layout";
import echartsdemo from "./components/EchartsDemo.vue";

const GridLayout = VueGridLayout.GridLayout;
const GridItem = VueGridLayout.GridItem;

import * as THREE from "three/build/three.module.js";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { Stats } from "three/examples/jsm/libs/stats.module.js";
import { FlyControls } from "three/examples/jsm/controls/FlyControls.js";
var demojpg = require("../static/img/demo.jpg");
var gonglu = require("../static/img/gonglu.jpg");
var mucong = require("../static/img/mucong.png");
var pulu = require("../static/img/pulu.jpg");
var scene, camera, renderer, spotLight, stats; //场景，相机，渲染器，光源
var raycaster, mouse, clock, controls; //光投射器，鼠标位置对s s应的二维向量
export default {
  data() {
    return {
      modal1: false,
      open1: false, //抽屉开关
      NameValue: "", //抽屉里name文本框的变量
      EchartsValue: "", //抽屉里图表文本框的变量
      layoutData: [], //容器集合（数据）
      dialogVisible: true, //父级容器属性的变量
      layoutHeight: 50, //容器的行高
      id: 0, //某个容器的内容大小该变时的id
      iid: "", //某个容器的内容大小该变时触发子组件的变量（容器内的图表组件）
      yulan: "", //图表配置文本框内容改变时触发子组件的变量（抽屉内的图表组件）
      yulanoption: "", //图表配置（抽屉内的图表组件）
      h: 0, //子组件的高
      controlsbool: true,
      projectiveObj: {}
    };
  },
  components: {
    GridLayout, //容器的父级
    GridItem, //容器
    echartsdemo //动态加载图表的组件
  },
  mounted() {
    //调用加载数据的方法
    this.init();
    //调用three加载方法
    this.initthree();
    //延迟函数初始化所有容器内的图表宽度问题
    setTimeout(() => {
      this.iid = "A-0";
    }, 100);
  },
  created() {
    //预览的组件属性初始化
    var yu =
      ' {"xAxis": {"type": "category","data": ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"]},"yAxis": {"type": "value"},"series": [{"data": [820, 932, 901, 934, 1290, 1330, 1320],"type": "line"}]}';
    this.yulanoption = JSON.parse(yu);
    document.getElementById("loader").remove();
  },
  methods: {
    registerComponent(name) {
      return import("./components/" + name);
    },
    //动态加载自定义组件
    init() {
      this.layoutData = layoutData.mainData;
      this.layoutData.forEach(item => {
        if (item.index == 1) {
          this.registerComponent(item.name).then(component => {
            const cpt = Vue.extend(component.default);
            new cpt({
              el: document.getElementById(item.name)
            });
          });
        }
      });
    },
    //点击添加模块触发
    initfang() {
      this.open1 = true; //打开抽屉
      this.yulan = "B-1123"; //打开抽屉初始化图表（宽度问题）
    },
    //抽屉内创建按钮点击事件
    initOK() {
      var i = {
        //添加容器的数据格式
        x: 20,
        y: 10,
        w: 20,
        h: 10,
        i: this.layoutData.length + 1,
        index: 0,
        name: "",
        option: {},
        style: "background:rgba(0,0,0,0.5);border-radius:5px;z-index:9"
      };
      i.name = this.NameValue; //赋值i里的name
      if (this.EchartsValue != "") {
        //如果文本框里有值
        i.option = JSON.parse(this.EchartsValue); //给容器的内容赋值
      }
      this.layoutData.push(i); //添加到容器的集合里
      this.open1 = false; //添加完成关闭抽屉
      this.NameValue = "";
      this.EchartsValue = "";
      setTimeout(() => {
        this.iid = "A-14857"; //重新渲染图表（解决初始化图表宽度问题）
      }, 100);
    },
    //某个容器的内容大小该变时触发
    layoutUpdatedEvent(newLayout) {
      newLayout.forEach(element => {
        if (element.i == this.id) {
          //遍历判断哪一个容器改变
          this.iid =
            "A-" + element.i + ":(" + element.w + "," + element.h + ")"; //给iid赋值（iid为被监听字段，值改变时会触发子组件）
        }
      });
    },
    //容器的内容大小该变时触发，返回具体容器id。
    resizedEvent(newLayout) {
      //此方法只能返回id，所以需要this.layoutUpdatedEvent来遍历返回对象
      this.id = newLayout; //给具体容器id赋值，提供给this.layoutUpdatedEvent判断
    },
    //抽屉内图表文本框内的值该变事件
    Echartsyulan() {
      console.log(this.EchartsValue);
      if (this.EchartsValue != "") {
        this.yulanoption = JSON.parse(this.EchartsValue); //把输入的图表配置赋值给子组件的属性
        this.yulan = "B-" + this.EchartsValue; //改变子组件的监听字段重新渲染图表
      }
    },
    zhuanhuan() {
      this.controlsbool = !this.controlsbool;
      this.initthree();
      console.log(this.controlsbool);
    },
    initthree() {
      scene = new THREE.Scene();
      camera = new THREE.PerspectiveCamera(
        25,
        window.innerWidth / window.innerHeight,
        50,
        1e7
      );
      renderer = new THREE.WebGLRenderer();
      renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
      renderer.setClearAlpha(0.2);
      renderer.setClearColor(new THREE.Color("#03213c"));
      renderer.setSize(window.innerWidth, window.innerHeight);
      let planeGeometry = new THREE.PlaneGeometry(1500, 1500, 1, 1);
      let planeMaterial = new THREE.MeshLambertMaterial({
        color: "#03213c",
        name: "planeGeometry"
      });
      let plane = new THREE.Mesh(planeGeometry, planeMaterial);
      plane.rotation.x = -0.5 * Math.PI;
      plane.position.y = 0;
      plane.position.z = 0;
      plane.castShadow = true;
      scene.add(plane);

      //添加光源
      spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 700, 0);
      spotLight.castShadow = true;
      spotLight.shadow.mapSize.width = 1024;
      spotLight.shadow.mapSize.height = 1024;
      spotLight.shadow.camera.near = 500;
      spotLight.shadow.camera.far = 4000;
      spotLight.shadow.camera.fov = 30;
      scene.add(spotLight);

      let ambientLight = new THREE.AmbientLight(0x3c3c3c);
      scene.add(ambientLight);

      // 调整摄像机位置
      camera.position.x = 0;
      camera.position.y = 500;
      camera.position.z = 2000;

      document.body.appendChild(renderer.domElement);
      renderer.render(scene, camera);

      raycaster = new THREE.Raycaster(); //光线投射器
      mouse = new THREE.Vector2(); //二维向量
      document.addEventListener(
        "mousemove",
        function() {
          event.preventDefault();
          mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
          mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
        },
        false
      );
      var $this = this;
      window.addEventListener(
        "click",
        function() {
          if (this.projectiveObj) {
            if (this.projectiveObj.material.name != "planeGeometry") {
              if (this.projectiveObj.hasChecked) {
                this.projectiveObj.hasChecked = false;
                // this.projectiveObj.material.color.set(0xffff00);
                $this.$Notice.success({
                  title: "信息",
                  desc: this.projectiveObj.material.name
                });
                //console.log(this.projectiveObj.material.name);
              } else {
                this.projectiveObj.hasChecked = true;
              }
            }
          }
        },
        false
      );

      /**
       * 根据光投射器判断鼠标所在向量方向是否穿过物体，穿过则改变物体材质
       * @param {*} raycaster 光投射器
       * @param {*} scene     场景
       * @param {*} camera    相机
       * @param {*} mouse     鼠标位置对应的二维向量
       */
      //this.renderRaycasterObj(raycaster, scene, camera, mouse);
      // function renderRaycasterObj(raycaster, scene, camera, mouse) {
      //   raycaster.setFromCamera(mouse, camera);

      //   var intersects = raycaster.intersectObjects(scene.children);
      //   if (intersects.length > 0) {
      //     var currentprojectiveObjT = intersects[0].object;
      //     if (this.projectiveObj != currentthis.projectiveObjT) {
      //       if (
      //         currentthis.projectiveObjT instanceof THREE.AxisHelper ||
      //         currentthis.projectiveObjT instanceof THREE.GridHelper
      //       ) {
      //         //穿过的是坐标轴线和网格线
      //         return;
      //       }
      //       // controls.autoRotate = false;
      //       if (intersects[0].object.material.name != "planeGeometry") {
      //         this.projectiveObj = intersects[0].object;
      //       }
      //     }
      //   } else {
      //     this.projectiveObj = null;
      //   }
      // }

      //添加正方体
      var data = require("../static/threedata.json");
      for (let i = 0; i < data.length; i++) {
        const dataitem = data[i];
        //console.log(dataitem);
        var count = 0;
        var img = demojpg;
        if (dataitem.imgtype === "1") {
          img = demojpg;
        } else if (dataitem.imgtype === "2") {
          img = gonglu;
        } else if (dataitem.imgtype === "3") {
          img = mucong;
        } else if (dataitem.imgtype === "4") {
          img = pulu;
        }
        var texture = new THREE.TextureLoader().load(img);
        texture.wrapS = THREE.RepeatWrapping;
        texture.wrapT = THREE.RepeatWrapping;
        if (dataitem.imgtype === "5") {
          texture.repeat.set(4, 1);
        }
        for (let x = 0; x < dataitem.louceng.length; x++) {
          const item = dataitem.louceng[x];
          //console.log(item);
          var material = new THREE.MeshBasicMaterial({
            map: texture,
            transparent: true,
            side: 2,
            //color: 0xffff00,
            name: item.name
          });

          var geometry = new THREE.BoxGeometry(
            dataitem.guige.x,
            item.gaodu,
            dataitem.guige.z
          );

          // var geometry = new THREE.BoxGeometry(20, 20, 20);
          var cube = new THREE.Mesh(geometry, material);
          if (dataitem.imgtype === "5") {
            count += item.gaodu;
            cube.translateY(count / 2);
            cube.translateX(dataitem.louweizhi.x);
            cube.translateZ(dataitem.louweizhi.z);
          } else {
            cube.translateY(count);
            cube.translateX(dataitem.louweizhi.x);
            cube.translateZ(dataitem.louweizhi.z);
            count += item.gaodu;
          }

          if (dataitem.xuanzhuan) {
            cube.rotateY(Math.PI / 2);
          }
          cube.castShadow = true;
          scene.add(cube);
        }
      }
      //拖拽配置
      if (this.controlsbool) {
        controls = new OrbitControls(camera, renderer.domElement);
        controls.autoRotate = true;
      } else {
        controls = new FlyControls(camera, renderer.domElement);
        controls.movementSpeed = 300;
        controls.domElement = renderer.domElement;
        controls.rollSpeed = Math.PI / 24;
        controls.autoForward = false;
        controls.dragToLook = false;
      }
      //实时刷新
      clock = new THREE.Clock();
      this.animate();
    },
    animate() {
      this.renderRaycasterObj(raycaster, scene, camera, mouse);
      renderer.render(scene, camera);
      requestAnimationFrame(this.animate);
      var delta = clock.getDelta();
      controls.update(delta);
      // scene.rotation.y -= 0.002;
    },
    renderRaycasterObj() {
      raycaster.setFromCamera(mouse, camera);
      var intersects = raycaster.intersectObjects(scene.children);
      if (intersects.length > 0) {
        var currentprojectiveObjT = intersects[0].object;
        if (this.projectiveObj != currentprojectiveObjT) {
          if (
            currentprojectiveObjT instanceof THREE.AxisHelper ||
            currentprojectiveObjT instanceof THREE.GridHelper
          ) {
            //穿过的是坐标轴线和网格线
            return;
          }
          // controls.autoRotate = false;
          if (intersects[0].object.material.name != "planeGeometry") {
            this.projectiveObj = intersects[0].object;
          }
        }
      } else {
        this.projectiveObj = null;
      }
    }
  }
};
</script>

<style>
.zhuan {
  position: absolute;
  top: 10px;
  left: 20px;
  z-index: 999;
}
#container {
  position: fixed;
  top: 0px;
  left: 0px;
}
.item-config {
  overflow: hidden;
}
.shezhiIcon {
  color: #fff;
  position: absolute;
  top: 0px;
  left: 0px;
}
</style>
