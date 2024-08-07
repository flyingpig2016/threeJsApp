<script setup lang="ts">
  // import HelloWorld from "./components/HelloWorld.vue";

  import * as THREE from "three";
  // 导入轨道控制器
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
  import diban from "./assets/machine/yinyuan1.webp";
  import mudiban from "./assets/machine/mudiban.jpg";
  import zuanshi from "./assets/machine/zuanshi.jpeg";
  import colors from "./assets/machine/colors.png";
  import bedroom from "./assets/environment/102714007卧室室内贴图.jpg";
  // 导入hdr加载器
  import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader.js";

  // 导入lil.gui
  import { GUI } from "three/examples/jsm/libs/lil-gui.module.min.js";

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
    //加载hdr图片
    let rgbeLoader = new RGBELoader();
    //加载纹理
    let texture = textureLoader.load(diban, function () {
      console.log("Texture loaded");
    });
    let aoMap = textureLoader.load(mudiban, (aoImg) => {
      console.log("ao贴图加载完毕", aoImg);
    });
    let alphaMap = textureLoader.load(zuanshi, (alimg) => {
      console.log("透明度贴图加载完成", alimg);
    });
    let lightMap = textureLoader.load(colors, (lImg) => {
      console.log("光照贴图加载完成", lImg);
    });

    //卧室环境贴图**********
    // textureLoader.load(bedroom, (alimg) => {
    //   console.log("透明度贴图加载完成", alimg);
    //   alimg.mapping = THREE.EquirectangularReflectionMapping;
    //   scene.background = alimg;
    // });

    // hdr幼儿园环境贴图**************
    // rgbeLoader.loadAsync("./environment/kindergarten.hdr").then((envMap) => {
    rgbeLoader.loadAsync("./environment/kindergarten.hdr").then((envMap) => {
      console.log("幼儿园图片加载完成", envMap);
      //设置球形贴图
      envMap.mapping = THREE.EquirectangularReflectionMapping;
      // 场景背景：若不为空，在渲染场景的时候将设置背景，且背景总是首先被渲染的。 可以设置一个用于的“clear”的Color（颜色）、一个覆盖canvas的Texture（纹理）， 或是 a cubemap as a CubeTexture or an equirectangular as a Texture。默认值为null。
      scene.background = envMap;
      // 设置环境贴图：若该值不为null，则该纹理贴图将会被设为场景中所有物理材质的环境贴图。 然而，该属性不能够覆盖已存在的、已分配给 MeshStandardMaterial.envMap 的贴图。默认为null。
      scene.environment = envMap;
      //设置plan环境贴图：通常是一个包含场景周围环境的反射或折射贴图
      planeMaterial.envMap = envMap;
    });

    // 纹理将会在水平和垂直方向上不断重复
    texture.wrapS = THREE.RepeatWrapping;
    texture.wrapT = THREE.RepeatWrapping;
    // texture.repeat.set(4, 4);

    // 创建一个平面
    let planeGeometry = new THREE.PlaneGeometry(5, 5);
    //创建材质
    let planeMaterial = new THREE.MeshBasicMaterial({
      color: 0xffffff,
      map: texture,
      side: THREE.DoubleSide,
      wireframe: false,
      // 允许透明
      transparent: true,
      // 设置ao贴图
      aoMap,
      aoMapIntensity: 0,
      //设置透明度贴图
      // alphaMap,
      // 设置光照贴图
      // lightMap,
    });
    let plane = new THREE.Mesh(planeGeometry, planeMaterial);
    const gui = new GUI();
    gui.add(planeMaterial, "aoMapIntensity").min(0).max(1).name("透明");

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
