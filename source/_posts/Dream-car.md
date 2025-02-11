---
title: Dream car
date: 2025-02-10 21:50:17
tags: Three.js
---
# Dream car 

### xiaomi SU7

![xiaomi SU7](https://s1.xiaomiev.com/activity-outer-assets/0328/images/su7/su7_1_20241226.jpg "SU7")
``` bash
<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { useRouter } from 'vue-router'
import * as THREE from 'three'
import { GUI } from 'three/examples/jsm/libs/lil-gui.module.min.js'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader.js'
import * as GeometryCompressionUtils from 'three/addons/utils/GeometryCompressionUtils.js';
import gsap from 'gsap'
import JSZip from 'jszip'
import JSZipUtils from 'jszip-utils'

const router = useRouter()
const width = window.innerWidth, height = window.innerHeight;
const threeContainer = ref(null);
const guiContainer = ref(null);

// 车辆外观
let wheelsCar, carBody, frontCar, hoodCar, backCar, glassCar;
const bodyMaterial = new THREE.MeshPhysicalMaterial({ 
  color: 0xff00ff, 
  roughness: 0.5, // 粗糙度
  metalness: 1, // 金属度
  clearcoat: 1, 
  clearcoatRoughness: 0 // 清漆
});

const wheelsCarMaterial = new THREE.MeshPhysicalMaterial({
  color: 0x000000, 
  roughness: 0.01, // 粗糙度
  metalness: 0.1, // 金属度
  transmission: 1
})

const glassCarMaterial = new THREE.MeshPhysicalMaterial({ 
  color: 0xffffff, 
  transmission: 1,
  roughness: 0, // 粗糙度
  metalness: 0, // 金属度
  transparent: true,
});

// 创建场景
const scene = new THREE.Scene();

// init WebGL渲染器
const renderer = new THREE.WebGLRenderer({
  antialias: true, // 抗锯齿
});
renderer.setSize( width, height );

// 设置色调映射
// renderer.toneMapping = THREE.ACESFilmicToneMapping;
// renderer.toneMappingExposure = 1; // 色调映射曝光
// renderer.outputEncoding = THREE.sRGBEncoding;

// init 相机
const camera = new THREE.PerspectiveCamera( 75, width / height, 0.1, 1000 );
camera.position.set( 0, 2, 6 );
camera.updateProjectionMatrix(); // 更新相机投影矩阵

// init 轨道控制器
const controls = new OrbitControls(camera, renderer.domElement)
controls.enableDamping = true; // 启用阻尼（惯性）

// 坐标辅助
// const axesHelper = new THREE.AxesHelper( 5 );
// scene.add(axesHelper);
// init 加载器gltf
const gltfLoader = new GLTFLoader();
// init 加载器draco
const dracoLoader = new DRACOLoader();
gltfLoader.setDRACOLoader(dracoLoader)
dracoLoader.setDecoderPath('/draco/gltf/');


gltfLoader.load('/su8-pipeline.glb', (gltf) => {
  gltf.scene.traverse(function (child) {
    const dreamCar = gltf.scene;
    dreamCar.traverse( child => {
      if ( child.isMesh ) {
        // console.log( child.name )
        // if ( child.name !== 'Object_45' ) { // 车身
        //   child.visible = false;
        // }
      
        // 车轮
        // if ( child.name === 'Object_56' ) {
        //   wheelsCar = child;
        //   wheelsCar.material = wheelsCarMaterial;
        // }

        // 玻璃
        if ( child.name === 'Object_21'
          || child.name === 'Object_40' 
          || child.name === 'Object_49'
          || child.name === 'Object_36') {
          glassCar = child;
          glassCar.material = glassCarMaterial;
        }

        // 车身
        if ( child.name === 'Object_18'
          || child.name === 'Object_32' 
          || child.name === 'Object_39'
          || child.name === 'Object_31'
          || child.name === 'Object_52'
          || child.name === 'Object_45'
        ) {
          carBody = child;
          carBody.material = bodyMaterial;
        }

        // Object_21:前后档玻璃 Object_45: 主驾门 Object_36：主驾车窗玻璃 Object_39：主驾后门 Object_40：主驾后门玻璃
        // Object_32: 副驾门 Object_49：副驾门玻璃 Object_52：副驾后门 Object_53：副驾后门玻璃 Object_52：后挡风玻璃
        
        if ( child.name === 'Object_56' ) {
          wheelsCar = child;
        }
        // if ( child.name === 'Object_19' ) {
        //   frontCar = child;
        // }
      }
    })
    scene.add(dreamCar);
  });
});
// const zipFile = await fetch('/su7.zip').then(response => response.arrayBuffer()); // 获取zip文件的二进制数据
// const new_zip = new JSZip(); // 实例化jszip
// JSZipUtils.getBinaryContent("/su7.zip", function (err, data) {
//     if (err) {
//       throw err; // or handle err
//     }

//     new_zip.loadAsync(data).then(function (res) { 
//       let fileList = res.files;
//       for (let key in fileList) {
//         console.log(key)
//         new_zip
//         .file(key)
//         .async("arraybuffer")
//         .then((content) => {
//           // Blob构造文件地址，通过url加载模型
//           let blob = new Blob([content]);
//           let modelUrl = URL.createObjectURL(blob);
//           console.log(modelUrl);
//           gltfLoader.load(modelUrl, (gltf) => {
//             gltf.scene.traverse(function (child) {
//               const dreamCar = gltf.scene;
//               dreamCar.traverse( child => {
//                 if ( child.isMesh ) {
//                   console.log( child.name )
//                   // if ( child.name !== 'Object_45' ) { // 车身
//                   //   child.visible = false;
//                   // }
                
//                   // 车轮
//                   if ( child.name === 'Object_56' ) {
//                     wheelsCar = child;
//                     wheelsCar.material = wheelsCarMaterial;
//                   }

//                   // 玻璃
//                   if ( child.name === 'Object_21'
//                     || child.name === 'Object_40' 
//                     || child.name === 'Object_49'
//                     || child.name === 'Object_36') {
//                     glassCar = child;
//                     glassCar.material = glassCarMaterial;
//                   }

//                   // 车身
//                   if ( child.name === 'Object_18'
//                     || child.name === 'Object_32' 
//                     || child.name === 'Object_39'
//                     || child.name === 'Object_31'
//                     || child.name === 'Object_52'
//                     || child.name === 'Object_45'
//                   ) {
//                     carBody = child;
//                     carBody.material = bodyMaterial;
//                   }

//                   // Object_21:前后档玻璃 Object_45: 主驾门 Object_36：主驾车窗玻璃 Object_39：主驾后门 Object_40：主驾后门玻璃
//                   // Object_32: 副驾门 Object_49：副驾门玻璃 Object_52：副驾后门 Object_53：副驾后门玻璃 Object_52：后挡风玻璃
                  
//                   if ( child.name === 'Object_56' ) {
//                     wheelsCar = child;
//                   }
//                   // if ( child.name === 'Object_19' ) {
//                   //   frontCar = child;
//                   // }
//                 }
//               })
//               scene.add(dreamCar);
//             });
//           });
//         });
//       }
//     })
//   })

// 添加光源 前、后、左、右、顶
const light1 = new THREE.DirectionalLight( 0xffffff, 1 );
light1.position.set( 0, 0, 10 );
scene.add( light1 );
const light2 = new THREE.DirectionalLight( 0xffffff, 1 );
light2.position.set( 0, 0, -10 );
scene.add( light2 );
const light3 = new THREE.DirectionalLight( 0xffffff, 1 );
light3.position.set( 0, 10, 0 );
scene.add( light3 );
const light4 = new THREE.DirectionalLight( 0xffffff, 1 );
light4.position.set( 10, 0, 0 );
scene.add( light4 );
const light5 = new THREE.DirectionalLight( 0xffffff, 1 );
light5.position.set( -10, 0, 0 );
scene.add( light5 );
const light6 = new THREE.DirectionalLight( 0xffffff, 1 );
light6.position.set( 10, 5, 0 );
scene.add( light6 );

const render = () => {
  controls.update();
  renderer.render( scene, camera );
  requestAnimationFrame( render );
}

renderer.setClearColor('#000');
scene.background = new THREE.Color('#ccc');
scene.environment = new THREE.Color('#ccc');

// 添加网格地面
const gridHelper = new THREE.GridHelper( 10, 10 );
gridHelper.material.opacity = 0.5;
gridHelper.material.transparent = true;
scene.add( gridHelper );

onMounted(() => {
  render();
  document.body.appendChild( renderer.domElement ); // 将渲染器添加到DOM中
  // init renderer`s backgroundColor
})

onBeforeUnmount(() => {
  memoryClean();
  // gui.destroy(); // 销毁GUI实例
})

const memoryClean = () =>{
    const meshes = [];
    scene.traverse(function (object) {
        object.isMesh && meshes.push(object);
    });
    for (let i = 0; i < meshes.length; i++) {
        const mesh = meshes[i];
        mesh.material.dispose();
        mesh.geometry.dispose();
        scene.remove(mesh);
    }
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
![xiaomi SU7](/image/website/xiaomiSU7.png)