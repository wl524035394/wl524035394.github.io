---
title: PonitLight
date: 2025-02-13 10:54:31
tags: 
- Three.js
- pointLight
category: WebGL
---

## 点光源

{% asset_img 1.jpg 点光源 %}

1. 灯光与阴影的关系与设置
2. 平行光阴影属性与阴影相机原理
3. 聚光灯各种属性与应用
4. 点光源属性与应用
``` bash
<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { useRouter } from 'vue-router'
import * as THREE from 'three'
import { GUI } from 'three/examples/jsm/libs/lil-gui.module.min.js'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
import gsap from 'gsap'

const router = useRouter()
const width = window.innerWidth, height = window.innerHeight;
const threeContainer = ref(null);
const guiContainer = ref(null);
// 创建场景
const scene = new THREE.Scene();
// WebGL渲染器
const renderer = new THREE.WebGLRenderer();
// 创建相机
const camera = new THREE.PerspectiveCamera( 75, width / height, 0.1, 1000 );
// 添加轨道控制器
const controls = new OrbitControls(camera, renderer.domElement)
// 坐标辅助
const axesHelper = new THREE.AxesHelper( 5 );
const smallBall = new THREE.Mesh(
  new THREE.SphereGeometry( 0.1, 20, 20 ), 
  new THREE.MeshBasicMaterial({ color: 0xff0000 })
);
 // 创建时钟
const clock = new THREE.Clock();
// 创建GUI
const gui = new GUI({
    width: 400, // 设置gui的宽度
    closeOnTop: true, // 设置gui是否在顶部关闭
    autoPlace: true, // 设置gui是否自动放置在页面中
    title: 'Controls', // 设置gui的标题
});

onMounted(() => {
  init()
})

onBeforeUnmount(() => {
  gui.destroy(); // 销毁GUI实例
})

const init = () => {
  camera.position.set( 0, 0, 10 );
  scene.add( camera );
  // camera.lookAt( 0, 0, 0 ); // 默认原点
  const sphereGeometry = new THREE.SphereGeometry(1, 64, 64);
  const material = new THREE.MeshStandardMaterial();
  const sphere = new THREE.Mesh( sphereGeometry, material );
  sphere.castShadow = true; // 设置物体投射阴影
  scene.add( sphere );
  // 创建平面
  const palneGeometry = new THREE.PlaneGeometry(50, 50)
  const plane = new THREE.Mesh(palneGeometry, material);
  plane.position.set(0, -1, 0);
  plane.rotation.x = -Math.PI / 2;
  plane.receiveShadow = true; // 设置物体接收阴影
  scene.add(plane);
  /* ---- 光源 ---- */
  // 环境光
  const light = new THREE.AmbientLight( 0xffffff, 0.75 );
  scene.add(light);

  smallBall.position.set( 2, 2, 2 );
  // 点光源
  const pointLight = new THREE.PointLight( 0xff0000, 0.75 )
  // pointLight.position.set( 2, 2, 2 );
  pointLight.castShadow = true; // 设置光照投射阴影 
  pointLight.target = smallBall
  pointLight.distance = 0;
  pointLight.decay = 0;
  smallBall.add( pointLight );
  scene.add( smallBall );
  gui.add( pointLight.position, 'x' ).min(-5).max(5).step(0.1);
  gui.add( pointLight, 'distance').min(0).max(1).step(0.01);
  gui.add( pointLight, 'decay' ).min(0).max(5).step(0.01);
  // 将坐标轴添加到场景中
  scene.add( axesHelper );  
  
  renderer.shadowMap.enabled = true; // 开启场景中的阴影贴图
  renderer.physicallyCorrectLights = true; // 开启物理正确的光照
  renderer.shadowMap.resolution = 1024; // 设置阴影贴图的分辨率
  renderer.shadowMap.type = THREE.PCFSoftShadowMap; // 设置阴影贴图的类型
  camera.aspect = width / height; // 更新相机宽高比
  camera.updateProjectionMatrix(); // 更新相机投影矩阵
  controls.enableDamping = true; // 启用阻尼（惯性）
  renderer.setAnimationLoop( animate );
  animate();
  // 将渲染器添加到DOM中
  threeContainer.value.appendChild( renderer.domElement );
  // 将GUI添加到页面中
  guiContainer.value.appendChild( gui.domElement );
}

const animate = () => {
  const time = clock.getElapsedTime();
  smallBall.position.x = Math.sin(time) * 2;
  smallBall.position.z = Math.cos(time) * 2;
  controls.update(); // 更新轨道控制器
  renderer.setSize( width, height );
  renderer.render( scene, camera ); // 将场景和相机渲染到屏幕上
  // requestAnimationFrame(animate);
}

const back = () => {
  router.go(-1)
}
</script>

<template>
  <div class="btn-group">
    <button class="back-btn"  @click="back">返回</button>
  </div>
  
  <div ref="threeContainer" class="threeContainer"></div>
  <div ref="guiContainer"></div>
</template>

``` 