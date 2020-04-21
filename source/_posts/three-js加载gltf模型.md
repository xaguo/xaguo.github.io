---
title: three.js加载gltf模型
date: 2017-05-06 10:55:18
tags: 随笔
---
three.js加载gltf模型Demo
<!-- more -->

1.从 [three.js - javascript 3D library](https://threejs.org/) 下载three.js。

2.新建项目，目录结构如图：

<img src="./cataloge.png" style="width=300px">

3.把 three.js 的 build 目录下的文件拷到项目 build 目录下，把 three.js/examples/js 下的文件拷到项目 js 目录下。

4.把 .gltf 模型文件放在项目 model 目录下。

5.在项目中编写 index.html

6.效果
<center>
<img src="./result.png" style="width=500px">
</center>

index.html代码：

	```html
	<!DOCTYPE html>
	<html lang="en">
	<head>
	   <title>three.js webgl - glTF</title>
	   <meta charset="utf-8">
	   <meta name="viewport" content="width=device-width, user-scalable=no, minimum-scale=1.0, maximum-scale=1.0">
	   <style>
	       body {
	           font-family: Monospace;
	           background-color: #222222;
	           margin: 0px;
	           overflow: hidden;
	       }
	       #container {
	           position: absolute;
	           top: 0px;
	           width:100%;
	           height:100%;
	           z-index: -1;
	       }
	   </style>
	</head>
	<body>
	<div id="container"></div>
	//引入js
	<script src="build/three.js"></script>
	<script src="js/controls/OrbitControls.js"></script>
	<script src="js/loaders/GLTF2Loader.js"></script>
	<script>
	   var orbitControls = null;
	   var container, camera, scene, renderer, loader;
	   var cameraIndex = 0;
	   var cameras = [];
	   var defaultCamera = null;
	   var gltf = null;
	   var mixer = null;
	   function onload() {
	       window.addEventListener( 'resize', onWindowResize, false );
	       document.addEventListener( 'keydown', function(e) { onKeyDown(e); }, false );
	       switchScene(0);
	       animate();
	   }
	   function initScene(index) {
	       container = document.getElementById( 'container' );
	       scene = new THREE.Scene();
	       defaultCamera = new THREE.PerspectiveCamera( 45, container.offsetWidth / container.offsetHeight, 1, 200 );
	       //defaultCamera.up = new THREE.Vector3( 0, 1, 0 );
	       scene.add( defaultCamera );
	       camera = defaultCamera;
	       var sceneInfo = sceneList[index];
	       var spot1 = null;
	       if (sceneInfo.addLights) {
	           var ambient = new THREE.AmbientLight( 0x222222 );
	           scene.add( ambient );
	           var directionalLight = new THREE.DirectionalLight( 0xdddddd );
	           directionalLight.position.set( 0, 0, 1 ).normalize();
	           scene.add( directionalLight );
	           spot1   = new THREE.SpotLight( 0xffffff, 1 );
	           spot1.position.set( 10, 20, 10 );
	           spot1.angle = 0.25;
	           spot1.distance = 1024;
	           spot1.penumbra = 0.75;
	           if ( sceneInfo.shadows ) {
	               spot1.castShadow = true;
	               spot1.shadow.bias = 0.0001;
	               spot1.shadow.mapSize.width = 2048;
	               spot1.shadow.mapSize.height = 2048;
	           }
	           scene.add( spot1 );
	       }
	       // 渲染
	       renderer = new THREE.WebGLRenderer({antialias:true});
	       renderer.setClearColor( 0x222222 );
	       renderer.setPixelRatio( window.devicePixelRatio );
	       renderer.setSize( window.innerWidth, window.innerHeight );
	       if (sceneInfo.shadows) {
	           renderer.shadowMap.enabled = true;
	           renderer.shadowMap.type = THREE.PCFSoftShadowMap;
	       }
	       container.appendChild( renderer.domElement );
	       var ground = null;
	       if (sceneInfo.addGround) {
	           var groundMaterial = new THREE.MeshPhongMaterial({
	               color: 0xFFFFFF,
	               shading: THREE.SmoothShading
	           });
	           ground = new THREE.Mesh( new THREE.PlaneBufferGeometry(512, 512), groundMaterial);
	           if (sceneInfo.shadows) {
	               ground.receiveShadow = true;
	           }
	           if (sceneInfo.groundPos) {
	               ground.position.copy(sceneInfo.groundPos);
	           } else {
	               ground.position.z = -0;
	           }
	           ground.rotation.x = -Math.PI / 2;
	           scene.add(ground);
	       }
		//加载gltf
	       loader = new THREE.GLTF2Loader();
	       var url = sceneInfo.url;
	       loader.load( url, function(data) {
	           gltf = data;
	           var object = gltf.scene !== undefined ? gltf.scene : gltf.scenes[0];
	           //status.innerHTML = "Load time: " + ( performance.now() - loadStartTime ).toFixed(2) + " ms.";
	           if (sceneInfo.cameraPos) {
	               defaultCamera.position.copy(sceneInfo.cameraPos);
	           }
	           if (sceneInfo.center) {
	               orbitControls.target.copy(sceneInfo.center);
	           }
	           if (sceneInfo.objectPosition) {
	               object.position.copy(sceneInfo.objectPosition);
	               if (spot1) {
	                   spot1.position.set(sceneInfo.objectPosition.x - 100, sceneInfo.objectPosition.y + 200, sceneInfo.objectPosition.z - 100);
	                   spot1.target.position.copy(sceneInfo.objectPosition);
	               }
	           }
	           if (sceneInfo.objectRotation) {
	               object.rotation.copy(sceneInfo.objectRotation);
	           }
	           if (sceneInfo.objectScale) {
	               object.scale.copy(sceneInfo.objectScale);
	           }
	           cameraIndex = 0;
	           switchCamera(cameraIndex);
	           var animations = gltf.animations;
	           if (animations && animations.length) {
	               mixer = new THREE.AnimationMixer(object);
	               for (var i = 0; i < animations.length; i++) {
	                   var animation = animations[i];
	                   if (sceneInfo.animationTime)
	                       animation.duration = sceneInfo.animationTime;
	                   mixer.clipAction(animation).play();
	               }
	           }
	           scene.add(object);
	           onWindowResize();
	       });
	       orbitControls = new THREE.OrbitControls(defaultCamera, renderer.domElement);
	   }
	   function onWindowResize() {
	       defaultCamera.aspect = container.offsetWidth / container.offsetHeight;
	       defaultCamera.updateProjectionMatrix();
	       var i, len = cameras.length;
	       for (i = 0; i < len; i++) { // just do it for default
	           cameras[i].aspect = container.offsetWidth / container.offsetHeight;
	           cameras[i].updateProjectionMatrix();
	       }
	       renderer.setSize( window.innerWidth, window.innerHeight );
	   }
	   function animate() {
	       requestAnimationFrame( animate );
	       render();
	   }
	   function render() {
	       renderer.render( scene, camera );
	   }
	   function onKeyDown(event) {
	       var chr = String.fromCharCode(event.keyCode);
	       if (chr == ' ') {
	           index = cameraIndex + 1;
	           if (index > cameras.length)
	               index = 0;
	           switchCamera(index);
	       } else {
	           var index = parseInt(chr);
	           if (!isNaN(index)	&& (index <= cameras.length)) {
	               switchCamera(index);
	           }
	       }
	   }
	//模型参数
	   var sceneList = [
	       {
	           name : "Demo", url : "models/gltf/5jiao/glTF/Model.gltf",//模型路径
	           cameraPos: new THREE.Vector3(0,0, 100),//相机位置
	           objectPosition: new THREE.Vector3(0, -50, 0),//模型位置
	           objectScale: new THREE.Vector3(0.2, 0.2, 0.2),//模型大小
	           objectRotation: new THREE.Euler(-Math.PI/5, 0,0),//模型旋转
	           addLights:false,//是否加光
	           shadows:false,//是否阴影
	           addGround:false,//是否加地面
	           extensions: ["glTF", "glTF-MaterialsCommon", "glTF-Binary"]
	       }
	   ];
	   function switchScene(index) {
	       cleanup();//
	       initScene(index);
	   }
	   function switchCamera(index) {
	       cameraIndex = index;
	       if (cameraIndex == 0) {
	           camera = defaultCamera;
	       }
	       if (cameraIndex >= 1 && cameraIndex <= cameras.length) {
	           camera = cameras[cameraIndex - 1];
	       }
	   }
	   function cleanup() {
	       if (container && renderer) {
	           container.removeChild(renderer.domElement);
	       }
	       cameraIndex = 0;
	       defaultCamera = null;
	       if (!loader || !mixer)
	           return;
	       mixer.stopAllAction();
	   }
	   onload();
	</script>
	</body>
	</html>
	```