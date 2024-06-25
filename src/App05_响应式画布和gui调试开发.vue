<script setup lang="ts">
  // import HelloWorld from "./components/HelloWorld.vue";

  import * as THREE from "three";
  // 导入轨道控制器
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

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

  //3. 创建渲染器
  const renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);

  //创建几何体
  const geometry = new THREE.BoxGeometry(1, 1, 1);
  console.log(geometry);
  //创建材质
  const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
  const parentMaterial = new THREE.MeshBasicMaterial({ color: 0xff0000 });
  parentMaterial.wireframe = true;

  //创建网格
  const cube = new THREE.Mesh(geometry, material);
  const parentCube = new THREE.Mesh(geometry, parentMaterial);

  parentCube.add(cube);
  parentCube.position.set(-3, 0, 0);
  cube.position.set(3, 0, 0); //有父元素就是相对坐标，没有父元素就是世界坐标

  //立方体放大缩小
  parentCube.scale.set(1, 1, 1);
  cube.scale.set(1, 1, 1);

  //绕x轴旋转
  // parentCube.rotation.x = Math.PI / 4;
  cube.rotation.x = Math.PI / 4; //叠加父元素的旋转

  //将网格添加到场景中
  // scene.add(cube);
  scene.add(parentCube);
  //设置相机位置
  camera.position.z = 10;
  camera.position.y = 2;
  camera.position.x = 2;
  camera.lookAt(0, 0, 0);

  //添加世界坐标辅助器
  const axesHelper = new THREE.AxesHelper(5);
  scene.add(axesHelper);

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

  // let renderIndex = 0; //渲染次数
  //4. 渲染函数
  function animate() {
    controls.update();
    // console.log(renderIndex++);
    requestAnimationFrame(animate);
    //旋转
    // cube.rotation.x += 0.01;
    // cube.rotation.y += 0.01;

    // 平移
    // let position = cube.position;
    // position.x += 0.01;
    // position.set(3, 0, 0);
    // console.log(cube.position.x);

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

  let eventObj = {
    Fullscreen: function () {
      document.body.requestFullscreen();
    },
    ExitFullscreen: function () {
      document.exitFullscreen();
    },
  };

  //创建GUI
  const gui = new GUI();
  //添加按钮
  gui.add(eventObj, "Fullscreen").name("全屏");
  gui.add(eventObj, "ExitFullscreen").name("退出全屏");
  // gui.add(cube.position, "x", -5, 5).name("立方体x轴位置");
  // 控制立方体位置
  let folder = gui.addFolder("立方体位置");
  folder
    .add(cube.position, "x")
    .min(-10)
    .max(10)
    .step(1)
    .name("立方体x轴位置")
    .onChange((val) => {
      console.log("立方体x轴位置" + val);
    })
    .onFinishChange((val) => {
      console.log("立方体x轴Finish位置" + val);
    });
  folder.add(cube.position, "y").min(-10).max(10).step(1).name("立方体y轴位置");
  folder.add(cube.position, "z").min(-10).max(10).step(1).name("立方体z轴位置");

  gui.add(parentMaterial, "wireframe").name("父元素线框模式");
  let colorParams = {
    cubeColor: "#ff0000",
  };
  gui
    .addColor(colorParams, "cubeColor")
    .name("立方体颜色")
    .onChange((val) => {
      cube.material.color.set(val);
    });
  // const btn = document.createElement("button");
  // btn.innerHTML = "点击全屏";
  // btn.style.position = "absolute";
  // btn.style.top = "10px";
  // btn.style.left = "10px";
  // btn.style.zIndex = "999";
  // document.body.append(btn);
  // btn.onclick = () => {
  //   //全屏
  //   renderer.domElement.requestFullscreen();
  // };

  // const exitBtn = document.createElement("button");
  // exitBtn.innerHTML = "退出全屏";
  // exitBtn.style.position = "absolute";
  // exitBtn.style.top = "10px";
  // exitBtn.style.left = "150px";
  // exitBtn.style.zIndex = "999";
  // document.body.append(exitBtn);
  // exitBtn.onclick = () => {
  //   //全屏
  //   document.exitFullscreen();
  // };
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
