<script setup lang="ts">
  // import HelloWorld from "./components/HelloWorld.vue";

  import * as THREE from "three";
  // 导入轨道控制器
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
  import diban from "./assets/machine/redleng.jpeg";

  // 导入lil.gui
  // import { GUI } from "three/examples/jsm/libs/lil-gui.module.min.js";

  //1. 创建场景
  const scene = new THREE.Scene();

  //2. 创建相机
  const camera = new THREE.PerspectiveCamera(
    45, //视角
    window.innerWidth / window.innerHeight, //宽高比
    0.1, //近平面
    1000 //远平面
  );
  //设置相机位置
  camera.position.z = 10;
  camera.position.y = 2;
  camera.position.x = 2;
  camera.lookAt(0, 0, 0);

  //3. 创建渲染器
  const renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);

  //添加世界坐标辅助器
  const axesHelper = new THREE.AxesHelper(5);
  scene.add(axesHelper);

  function createPlan() {
    //创建纹理加载器
    let textureLoader = new THREE.TextureLoader();
    //加载纹理
    let texture = textureLoader.load(diban, function () {
      console.log("Texture loaded");
    });
    texture.wrapS = THREE.RepeatWrapping;
    texture.wrapT = THREE.RepeatWrapping;
    // texture.repeat.set(4, 4);

    let planeGeometry = new THREE.PlaneGeometry(5, 5);
    //创建材质
    let planeMaterial = new THREE.MeshBasicMaterial({
      color: 0xffffff,
      map: texture,
    });
    let plane = new THREE.Mesh(planeGeometry, planeMaterial);

    scene.add(plane);
  }

  createPlan();

  // 轨道控制器
  function createOribt() {
    // 添加轨道控制器
    const controls = new OrbitControls(camera, renderer.domElement);
    // const controls = new OrbitControls(camera, document.body); //会阻止点击事件
    console.log(renderer);

    //设置带阻尼的惯性
    controls.enableDamping = true;
    //设置阻尼系数
    controls.dampingFactor = 0.05;
    //设置旋转系数
    controls.autoRotate = false;
    controls.autoRotateSpeed = 1;
    return controls;
  }
  const controls = createOribt();

  // let renderIndex = 0; //渲染次数
  //4. 渲染函数
  function animate() {
    controls.update();
    // console.log(renderIndex++);
    requestAnimationFrame(animate);
    //渲染
    renderer.render(scene, camera);
  }
  animate();

  //监听窗口变化
  window.addEventListener("resize", () => {
    //重置渲染宽高比
    renderer.setSize(window.innerWidth, window.innerHeight);
    //重置相机宽高比
    camera.aspect = window.innerWidth / window.innerHeight;
    //更新相机投影矩阵
    camera.updateProjectionMatrix();
  });
</script>

<template>
  <div></div>
  <!-- <HelloWorld msg="Vite + Vue" /> -->
</template>

<style>
  * {
    margin: 0;
    padding: 0;
  }

  canvas {
    display: block;
    position: fixed;
    width: 100vw;
    height: 100vh;
    top: 0;
    left: 0;
  }
</style>
