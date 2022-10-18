---
title: three.js开发指南 笔记
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---


### 准备

```js
1、准备
-Three.js自身也提供了在线场景编辑器，访问地址为http://threejs.org/editor。使用该编辑器，可以用图形化方法创建Three.js场景。


-Three.js 开发指南示例代码
https://github.com/josdirksen/learning-threejs

-禁用Firefox和Chrome的安全性检测
	如果你使用的是Chrome浏览器，有种方式可以禁用安全性设置，这样就可以在没有Web服务器的情况下查看示例。需要注意的是，应尽量避免用下面的方法访问真正的网站，因为这样做会使浏览器向各种恶意代码敞开大门。用下面的命令可以启动Chrome浏览器同时禁用所有安全特性。
    对于Windows操作系统：chrome.exe --disable-web-security
    对于Linux操作系统：gogle-chrome --disable-web-security
    对于Mac OS操作系统：open -a "Google Chrome" --args --disable-web-security
通过这种方式启动Chrome浏览器就可以直接运行本地文件系统中的示例。
```

### 实例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Example - Baseic skeleton</title>
    <script src="./js/three.js"></script>
    <script src="./js/TrackballControls.js"></script>
    <!-- 主要用于检测动画运行时的帧数。在动画运行时，该库可以在一个图片中显示画面每秒传输帧数 -->
    <script src="./js/Stats.js"></script>
    <!-- 实时调整动画参数 -->
    <script src="./js/dat.gui.js"></script>
    <script src="./js/util.js"></script>

</head>
<body>
    <div id="webgl-output"></div>

    <script>
        // 
        var camera, renderer, scene;
        function init() {
            
            // 初始化状态栏
            var stats = initStats()
            // 创建一个场景，该场景将包含所有元素，例如对象、相机和灯光。
            scene = new THREE.Scene();

            // 创建一个摄像头，它定义了我们要看的地方
            camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);

            // 创建渲染并设置大小
            renderer = new THREE.WebGLRenderer();
            // 将场景的背景颜色设置为接近黑色
            renderer.setClearColor(new THREE.Color(0x000000));
            // 设置场景的大小
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.shadowMap.enabled = true

            // 创建了坐标轴（axes）对象并设置轴线的粗细值为20
            var axes = new THREE.AxesHelper(20);
            //  在屏幕上显示轴
            scene.add(axes);

            // 创建平面 宽60 高20
            var planeGeometry = new THREE.PlaneGeometry(60, 20);
            // 创建材质对象来设置平面的外观
            var planeMaterial = new THREE.MeshLambertMaterial({
                color: 0xffffff
            })

            // 将大小和外观组合进Mesh对象并赋值给平面变量
            var plane = new THREE.Mesh(planeGeometry, planeMaterial);
            plane.receiveShadow = true
            // 旋转并定位平面
            plane.rotation.x = -0.5 * Math.PI;
            plane.position.set(15, 0, 0);

            // 将平面添加到场景中
            scene.add(plane);

            // 创建一个立方体
            var cubeGeometry = new THREE.BoxGeometry(4, 4, 4);
            // 为立方体添加材质
            var cubeMaterial = new THREE.MeshLambertMaterial({
                color: 0xFF0000,
            });

            var cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
            cube.castShadow = true
            // 定位立方体
            cube.position.set(-4, 3, 0);

            // 将立方体添加到场景中
            scene.add(cube);

            // 创建一个球体
            var sphereGeometry = new THREE.SphereGeometry(4, 20, 20);
            var sphereMaterial = new THREE.MeshLambertMaterial({
                color: 0x7777ff,
            });

            var sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
            sphere.castShadow = true
            // 定位球体
            sphere.position.set(20, 4, 2);

            // 将球体添加到场景中
            scene.add(sphere);

            // 将相机定位并指向场景中心
            camera.position.set(-30, 40, 30);
            // 摄像机将决定哪些东西会被渲染到场景中。在这段代码中，我们使用x、y、z的位置属性来设置摄像机的位置。
            // 为了确保所要渲染的物体能够被摄像机拍摄到，我们使用lookAt方法指向场景的中心，默认状态下摄像机是指向（0，0，0）位置的
            camera.lookAt(scene.position);


            // 为阴影添加聚光灯
            var spotLight = new THREE.SpotLight(0xffffff);
            spotLight.position.set(-40, 40, -15);
            spotLight.castShadow = true;
            spotLight.shadow.mapSize = new THREE.Vector2(1024, 1024);
            spotLight.shadow.camera.far = 130;
            spotLight.shadow.camera.near = 40;
            scene.add(spotLight);


            // 添加微妙的环境照明
            var ambientLight = new THREE.AmbientLight(0x353535);
            scene.add(ambientLight);

            // 将渲染器的输出添加到html元素
            document.getElementById("webgl-output").appendChild(renderer.domElement);

            // 渲染场景
            renderer.render(scene, camera);
            //初始化控制器
            var trackballControls = initTrackballControls(camera, renderer)
            var clock = new THREE.Clock()

            // 定义属性，默认值
            var controls = new function() {
                this.rotationSpeed = 0.02 // 立方体旋转速度
                this.bouncingSpeed = 0.02 // 球体弹跳速度
            }
            console.log('controls:', controls, dat);
            // 需要将这个JavaScript对象传递给data.GUI对象，并设置这两个属性的取值范围
            var gui = new dat.GUI()
            gui.add(controls, 'rotationSpeed', 0, 0.5)
            gui.add(controls, 'bouncingSpeed', 0, 0.5)


            renderScene()
            
            // 动画
            var step = 0;
            function renderScene() {
                stats.update()
                // TrackballControl.js库也需要在render函数渲染完一帧后更新
                trackballControls.update(clock.getDelta())

                cube.rotation.x += controls.rotationSpeed
                cube.rotation.y += controls.rotationSpeed
                cube.rotation.z += controls.rotationSpeed
                // cos 函数返回一个数值的余弦值
                step += controls.bouncingSpeed
                sphere.position.x = 20 + 10*(Math.cos(step))
                sphere.position.y = 2 + 10*Math.abs(Math.sin(step))
                
                requestAnimationFrame(renderScene)
                renderer.render(scene, camera);
            }
        }

        window.addEventListener('resize', onResize, false)
        function onResize() {
            camera.aspect = window.innerWidth / window.innerHeight
            camera.updateProjectionMatrix()
            renderer.setSize(window.innerWidth, window.innerHeight)
        }
        // 初始化
        init()

       </script>
</body>
</html>

-threejs

-控制器库TrackballControls.js。
	-有了它便可以利用鼠标任意移动摄像机，以便从不同角度观察场景

在代码中，首先定义了场景（scene）、摄像机（camera）和渲染器（renderer）对象。
	1、场景是一个容器，主要用于保存、跟踪所要渲染的物体和使用的光源。如果没有THREE.Scene对象，那么Three.js就无法渲染任何物体，在第2章还会对THREE.Scene进行详细介绍。示例中所要渲染的方块和球体稍后将会添加到场景对象中。
	
	2、还定义了摄像机对象，摄像机决定了能够在场景看到什么。
		你还会进一步了解摄像机对象能够接受的参数。
    3、我们定义了渲染器对象
		该对象会基于摄像机的角度来计算场景对象在浏览器中会渲染成什么样子。最后WebGLRenderer将会使用电脑显卡来渲染场景。

-dat.GUI

	在创建类似三维场景和动画时，我们需要尝试很多次才能够确定最合适的速度和颜色。如果有一个GUI（可视化图形界面）允许我们在运行期间修改这些属性，那么事情就会变得很简单。幸运的是，这样的GUI是存在的
```

## three.js基础知识

<!--more-->

```js
一个场景想要显示任何东西，需要三种类型的组件

摄像机
	-决定屏幕上哪些东西西药渲染
光源
	-决定材质如何显示以及用于产生阴影
对象
	-他们是摄像机透视图主要渲染对象，如方块、球体
渲染器
	-基于摄像机和场景提供的信息，调用底层图形API执行真正的场景绘制工作
	
◆THREE.Scene对象
	有时被称为场景图，可以用来保存所有图形场景的必要信息。在Three.js中，这意味着THREE.Scene保存所有对象、光源和渲染所需的其他对象。有趣的是，场景图，顾名思义，不仅仅是一个对象数组，还包含了场景图树形结构中的所有节点。每个你添加到Three.js场景的对象，甚至包括THREE.Scene本身，都是继承自一个名为THREE.Object3D的对象。一个THREE.Object3D对象也可以有自己的子对象，你也可以使用它的子对象来创建一个Three.js能解释和渲染的对象树。
	
	
THREE.Mesh对象（你看到的平面，网格对象）
THREE.SpotLight对象（聚光灯光源）
THREE.AmbientLight对象（环境光）

场景的方法
THREE.Scene.Add：
	-用于向场景中添加对象
THREE.Scene.Remove：
	-用于移除场景中的对象
THREE.Scene.children：
	-用于获取场景中所有的子对象列表
THREE.Scene.getObjectByName：
	-利用name属性，用于获取场景中特定的对象这些方法是和场景相关的重要方法，通常情况下这些方法就可
THREE.Mesh
	-网格
THREE.Geometry
	-几何体

◆网格对象的属性和方法
▪position
	-该属性决定该对象相对于父对象的位置。通常父对象是THREE.Scene 对象/THREE.Object3D 对象
    -修改1  cube.position.set(10,3,1)
	-修改2  cube.position = new THREE.Vector3(10,3,1)
▪rotation(x,y,z)
	-该属性设置每个轴的旋转弧度。threejs还提供了特定轴旋转弧度的方法。rotateX、rotateY、rotateZ
	-修改1：cube.rotation.x = 0.5*Math.PI
	-修改2：cube.rotation.set(0.5*Math.PI, 0, 0)
	-修改3：new THREE.Vector3(0.5*Math.PI, 0, 0)
▪scale(x,y,z)
	-设置沿 x,y,z轴缩放对象
▪translate[X/Y/Z](m)
	-沿某轴平移 m 距离
▪visible
	-值为false时，THREE.Mesh 将不会被渲染到场景
	
◆摄像机
	透视投影摄像机使用接近真实世界的视角来渲染场景，而正交投影摄像机提供了一种在游戏中被广泛采用的伪三维效果
	-分类： 正交投影摄像机和透视投影摄像机
    -开启WebVR renderer.vr.enabled=true
    
    
1、给场景添加雾化效果
// 参数1：颜色，参数2：浓度 需要注意的是，该方法中雾的浓度是随着距离呈指数增长。
scene.fog = new THREE.FogExp2(0xffffff, 0.01)

2、overrideMaterial
	// 当设置了overrideMaterial属性后，场景中所有的物体都会使用该属性指向的材质，即使物体本身也设置了材质。当某一个场景中所有物体都共享同一个材质时，使用该属性可以通过减少Three.js管理的材质数量来提高运行效率，但是实际应用中，该属性通常并不非常实用。

scene.overrideMaterial = new THREE.MeshLamberMaterial({color: 0xffffff})

3、场景元素的遍历
// 传递来的方法将会在每一个子对象上执行
scene.traverse(function(obj){
    if(obj instanceof THREE.Mesh && obj != plane) {
        ...
    }
})

4、自定义几何体
// 对于渲染器和游戏引擎来说，使用三角形创建面更加容易，因为三角形渲染起来效率更高
var vertices = [
      new THREE.Vector3(1, 3, 1),
      new THREE.Vector3(1, 3, -1),
      new THREE.Vector3(1, -1, 1),
      new THREE.Vector3(1, -1, -1),
      new THREE.Vector3(-1, 3, -1),
      new THREE.Vector3(-1, 3, 1),
      new THREE.Vector3(-1, -1, -1),
      new THREE.Vector3(-1, -1, 1)
  ];

  var faces = [
      new THREE.Face3(0, 2, 1),
      new THREE.Face3(2, 3, 1),
      new THREE.Face3(4, 6, 5),
      new THREE.Face3(6, 7, 5),
      new THREE.Face3(4, 5, 1),
      new THREE.Face3(5, 0, 1),
      new THREE.Face3(7, 6, 2),
      new THREE.Face3(6, 3, 2),
      new THREE.Face3(5, 7, 0),
      new THREE.Face3(7, 2, 0),
      new THREE.Face3(1, 3, 4),
      new THREE.Face3(3, 6, 4),
  ];

  var geom = new THREE.Geometry();
  geom.vertices = vertices;
  geom.faces = faces;
  geom.computeFaceNormals();
    
  function render() {
    ...  
      mesh.children.forEach(function (e) {
        // 组成网格的几何体的vertices属性值指向一个更新后的顶点数组
          e.geometry.vertices = vertices;
        // 告诉几何体顶点需要更新
          e.geometry.verticesNeedUpdate = true;
        // 重新计算每个面
          e.geometry.computeFaceNormals();
          // delete e.geometry.__directGeometry
      });

      // render using requestAnimationFrame
      requestAnimationFrame(render);
      renderer.render(scene, camera);
  }
	// 顶点顺序决定了某个面是面向摄像机还是背向摄像机的。如果你想创建面向摄像机的面，那么顶点的顺序是顺时针的，反之顶点的顺序是逆时针的。
    
5、clone 几何体
// 克隆
var clonedGeometry = mesh.children[0].geometry.clone();
// 新材质
var materials = [
  new THREE.MeshLambertMaterial({opacity: 0.8, color: 0xff44ff, transparent: true}),
  new THREE.MeshBasicMaterial({color: 0x000000, wireframe: true})// 线框
];
// 使用多种材质来创建网格
var mesh2 = THREE.SceneUtils.createMultiMaterialObject(clonedGeometry, materials);
// 请记住，mesh 变量包含两个THREE.Mesh子对象：基于两个不同材质创建的。通过这个复制的几何体我们创建了一个新的网格，并命名为mesh2
mesh2.children.forEach(function (e) {
  // 几何体开启投影
  e.castShadow = true
});

// 网格位移
mesh2.translateX(5);
mesh2.translateZ(5);
mesh2.name = "clone";
// 添加到场景时删除之前的 mesh2, 如果有的话
scene.remove(scene.getChildByName("clone"));
scene.add(mesh2);
    
6、为几何体添加线框
// geom 为一个几何体对象名称
var wireframe = new THREE.WireframeGeometry(geom)
// 基于新建的线框对象创建一个Three.LineSegments对象并将它添加到场景中
var line = new THREE.LineSegments(wireframe)
scene.add(line)
// 设置线框的宽度
line.material.linewidth = 2
```

#### THREE.Color 颜色类的方法

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/three.color.jpg)

### 创建基础对象

```js
创建颜色
	-其中可以传入一个颜色 十六进制、rgb(r,g,b,)、hls、颜色英文
    -分离的RGB值 三个值的范围都是0到1。形式例如newTHREE.Color(1，0，0)
    new THREE.Color()这种构造形式会创建一个代表白颜色的对象
    
创建一个场景
	scene = new THREE.Scene()

创建一个立方体
   - BoxGeometry(长宽高)
    var cubeGeometry = new THREE.BoxGeometry(4, 4, 4);

为立方体添加材质
    var cubeMaterial = new THREE.MeshLambertMaterial({
        color: 0xFF0000,
    });

创建一个网格
	- Mesh(几何体, 材质) // 材质可以为数组形式
    var mesh1 = new THREE.Mesh(cubeGeometry, cubeMaterial)

创建一个正交相机
	var camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);

创建了坐标轴（axes）对象并设置轴线的粗细值为20
    var axes = new THREE.AxesHelper(20);

创建二维点
	new THREE.Vector2(x, y)

创建三维点
	new THREE.Vector3(x, y, z)

创建平面 宽60 高20
    var planeGeometry = new THREE.PlaneGeometry(60, 20);
```



### 相机

#### 透视相机

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/%E6%A8%AA%E5%90%91%E8%A7%86%E5%9C%BA.png)

![]()![透视相机](https://raw.githubusercontent.com/wyf195075595/images/main/blog/%E9%80%8F%E8%A7%86%E7%9B%B8%E6%9C%BA.jpg)

#### 正交相机

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/%E6%AD%A3%E4%BA%A4%E8%A7%86%E5%9C%BApng.png)

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/%E6%AD%A3%E4%BA%A4%E7%9B%B8%E6%9C%BA.jpg)

#### 相机切换控制器

```js
  var trackballControls
  var controls = new function () {
      this.perspective = "Perspective";
      this.switchCamera = function () {
          if (camera instanceof THREE.PerspectiveCamera) {
              // 正交摄影机
              camera = new THREE.OrthographicCamera(window.innerWidth / -16, window.innerWidth / 16, window.innerHeight / 16, window.innerHeight / -16, -200, 500);
              camera.position.x = 120;
              camera.position.y = 60;
              camera.position.z = 180;
              camera.lookAt(scene.position);

              trackballControls = initTrackballControls(camera, renderer);
              this.perspective = "Orthographic";
          } else {
              // 透视摄像机
              camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
              camera.position.x = 120;
              camera.position.y = 60;
              camera.position.z = 180;

              

              camera.lookAt(scene.position);
              trackballControls = initTrackballControls(camera, renderer);
              this.perspective = "Perspective";
          }
      };
  };

  var gui = new dat.GUI();
  gui.add(controls, 'switchCamera');
  gui.add(controls, 'perspective').listen();

  // make sure that for the first time, the
  // camera is looking at the scene
  camera.lookAt(scene.position);

  trackballControls = initTrackballControls(camera, renderer);
  var clock = new THREE.Clock();

  render();

  function render() {
      trackballControls.update(clock.getDelta());
      stats.update();

      // render using requestAnimationFrame
      requestAnimationFrame(render);
      renderer.render(scene, camera);
  }
```

#### 切换相机视角

```js
camera.lookAt(new THREE.Vector3(x, 10, 0));
```

### 光源

> 没有光源，渲染的场景将不可见（除非你使用基础材质或线框材质）
>
> -Three.js中可用的光源。
>
> -特定光源使用的时机。
>
> -如何调整和配置所有光源的行为。
>
> -简单地介绍如何创建镜头光晕。

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/%E5%85%89%E6%BA%90.png)

#### ◆THREE.PointLight、THREE.SpotLight和THREE.DirectionalLight的主要区别

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/gy_type.png)

> THREE.PointLight从特定的一点向所有方向发射光线。
>
> THREE.SpotLight从特定的一点以锥形发射光线。
>
> THREE.DirectionalLight不是从单个点发射光线，而是从二维平面发射光线，光线彼此平行。

#### 简单的光源

```js
前四个为简单基础光源。所有这些光源都是基于THREE.Light对象扩展的，这个对象提供公用的功能

1、THREE.AmbientLight
	在创建THREE.AmbientLight（点光源）时，颜色将会应用到全局。该光源并没有特别的来源方向，并且THREE.AmbientLight不会生成阴影。通常，不能将THREE.AmbientLight作为场景中唯一的光源，因为它会将场景中的所有物体渲染为相同的颜色，而不管是什么形状
	在使用其他光源同时使用它，目的是弱化阴影或给场景添加一些额外的颜色

◆控制 AmbientLight 光源
    // add ambient lighting
      var ambientLight = new THREE.AmbientLight("#606008", 1);
      scene.add(ambientLight);

	  // add controls
      var controls = setupControls();

	  function setupControls() {
        var controls = new function () {
          this.intensity = ambientLight.intensity;
          this.ambientColor = ambientLight.color.getStyle();
          this.disableSpotlight = false;
        };

        var gui = new dat.GUI();
        gui.add(controls, 'intensity', 0, 3, 0.1).onChange(function (e) {
          ambientLight.color = new THREE.Color(controls.ambientColor);
          ambientLight.intensity = controls.intensity;
        });
        gui.addColor(controls, 'ambientColor').onChange(function (e) {
          ambientLight.color = new THREE.Color(controls.ambientColor);
          ambientLight.intensity = controls.intensity;
        });
        gui.add(controls, 'disableSpotlight').onChange(function (e) {
          spotLight.visible = !e;
        });

        return controls;
      }

2、THREE.SpotLight

    THREE.SpotLight（聚光灯光源）是最常使用的光源之一（特别是如果你想要使用阴影的话）
该光源产生的光具有方向和角度
	THREE.SpotLight的shadow属性为enable时，可以调节阴影特性 
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/SpotLight.enable.png)

```js
创建聚合灯光源


  var spotLight = new THREE.SpotLight("#ffffff");
  spotLight.position.set(-40, 60, -10);
  spotLight.castShadow = true;
  spotLight.shadow.camera.near = 1;
  spotLight.shadow.camera.far = 100;
  spotLight.target = plane;// 让这个光源照向指定的方向 plane 为一个几何体
  
  //distance设置光锥的长度
  spotLight.distance = 0;
  //angle属性定义了光锥的形状
  spotLight.angle = 0.4;
  spotLight.shadow.camera.fov = 120;
  scene.add(spotLight);

// 如果不想让光照在某个几何体，而是某个点

var target = new THREE.Object3D()
target.position = new THREE.vertor3(5,0,0)

//THREE.CameraHelper可以非常方便地调试阴影
var debugCamera = new THREE.CameraHelper(spotLight.shadow.camera)
scene.add(debugCamera)

// THREE.SpotLightHelper调试灯光
var helper = new THREE.SpotLightHelper(spotLight)
scene.ad(helper)

//如果想要阴影更柔和
	可以在THREE.WebGLRenderer对象上设置不同的shadowMap-Type属性值。默认情况下，此属性的值为THREE.PCFShadowMap；如果将此属性设置为PCFSoftShadowMap，则会得到更柔和的阴影。
```



#### THREE.SpotLight的所有属性

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/SpotLight.png)



```js
3、 THREE.PointLight
THREE.PointLight（点光源）是一种单点发光、照射所有方向的光源

创建 THREE.PointLight

  // pointColor 还可以是 数字或THREE.Color对象
  var pointColor = "#ccffcc";
  var pointLight = new THREE.PointLight(pointColor);
  pointLight.decay = 0.1
  pointLight.castShadow = true;

  scene.add(pointLight);
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/PointLight.png)

```js
4、THREE.DirectionalLight
THREE.DirectionalLight（平行光）。它发出的所有光线都是相互平行的.平行光的一个范例就是太阳光

// 创建

  var pointColor = "#ff5808";
  var directionalLight = new THREE.DirectionalLight(pointColor);
  directionalLight.position.set(-40, 60, -10);
  directionalLight.castShadow = true;
  directionalLight.shadow.camera.near = 2;
  directionalLight.shadow.camera.far = 80;
  directionalLight.shadow.camera.left = -30;
  directionalLight.shadow.camera.right = 30;
  directionalLight.shadow.camera.top = 30;
  directionalLight.shadow.camera.bottom = -30;

  directionalLight.intensity = 0.5;
  directionalLight.shadow.mapSize.width = 1024;
  directionalLight.shadow.mapSize.height = 1024;

  scene.add(directionalLight);

// THREE.DirectionalLight对象和THREE.SpotLight对象有许多属性相同，
例如:position、target、intensity、castShadow、shadow.camera.near、shadow.camera.far、shadow.mapSize.width、shadow.mapSize.height和shadow.bias。
```

#### 特殊光源

```js
1、THREE.HemisphereLight
	可以创建出更加贴近自然的户外光照效果
    如果不使用这个灯光，要模拟户外光照，可以创建一个THREE.DirectionalLight来模拟太阳光，并且可能再添加一个THREE.AmbientLight来为场景提供基础色。但是，这样的光照效果看起来并不怎么自然。在户外，并不是所有的光照都来自上方：很多是来自于大气的散射和地面以及其他物体的反射。Three.js中的THREE.HemisphereLight光源就是为这种情形创建的。它为获得更自然的户外光照效果提供了一种简单的方式

// 创建 HemisphereLight(来自天空的颜色, 来自地面的颜色, 光照强度)
  var hemiLight = new THREE.HemisphereLight(0x0000ff, 0x00ff00, 0.6);
  hemiLight.position.set(0, 500, 0);
  scene.add(hemiLight);

// hemiLight属性
-groundColor	从地面发出的光线颜色
-color			从天空发出的颜色
-intensity		光线照射的强度


2、THREE.AreaLight
	可以定义一个长方形的发光区域
  // 需要额外引入库 RectAreaLightUniformsLib.js
    
  RectAreaLight(颜色, 光强, 宽, 高)
  var areaLight1 = new THREE.RectAreaLight(0xff0000, 500, 4, 10);
  areaLight1.position.set(-10, 10, -35);
  scene.add(areaLight1);

3、THREE.LensFlare
	// 需额外引入 Lensflare.js
	镜头光晕
  flare = new THREE.Lensflare(texture, size, distance, blending, color, opacity)

// 加载纹理
var textureFlare0 = THREE.ImageUtils.loadTexture("../../assets/textures/flares/lensflare0.png");
  var textureFlare3 = THREE.ImageUtils.loadTexture("../../assets/textures/flares/lensflare3.png");
// 定义镜头光晕的颜色
  var flareColor = new THREE.Color(0xffaacc);
// 创建THREE.LensFlare对象（主光晕）
  var lensFlare = new THREE.Lensflare();
  // 副光晕 add(纹理,尺寸,距离,混合模式)
  lensFlare.addElement(new THREE.LensflareElement(textureFlare0, 350, 0.0, flareColor));
  lensFlare.addElement(new THREE.LensflareElement(textureFlare3, 60, 0.6, flareColor));
  lensFlare.addElement(new THREE.LensflareElement(textureFlare3, 70, 0.7, flareColor));
  lensFlare.addElement(new THREE.LensflareElement(textureFlare3, 120, 0.9, flareColor));
  lensFlare.addElement(new THREE.LensflareElement(textureFlare3, 70, 1.0, flareColor));
// 给聚光灯添加镜头光晕 
spotLight.add(lensFlare);
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/THREE.Lensflare.jpg)

### 材质

> Three.js 材质就像物体的皮肤，决定了几何体的外表

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/material.jpg)

#### 材质的共性

```js
基础属性：图1
	这些属性是最常用的。通过这些属性，可以控制物体的不透明度、是否可见以及如何被引用（通过ID或是自定义名称）。
	
融合属性：图2
	每个物体都有一系列的融合属性。这些属性决定了物体如何与背景融合。
	
高级属性：图3
	有一些高级属性可以控制底层WebGL上下文对象渲染物体的方式。大多数情况下是不需要使用这些属性的。
    它们与WebGL内部如何工作相关。如果你想更多地了解这些属性，那么OpenGL规范是一个很好的起点。可以在下面这个网址找到此规范：http://www.khronos.org/registry/gles/specs/2.0/es_full_spec_2.0.25.pdf
```

##### 基础属性![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/material_base.jpg)

##### 融合属性

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/material_rh.jpg)

##### 高级属性

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/material_gj.jpg)

#### 简单网格材质

```js
//MeshBasicMaterial、MeshDepthMaterial和MeshNormal-Material

1、两种创建网格方式
	-参数对象传入
	var material = new THREE.MeshBasicMaterial({
        color: 0xff0000,
        name: 'material-1',
        opacity: 0.5,
        transparency: true
        ...
    })
    
    -创建实例分别设置属性
    var material = new THREE.MeshBasicMaterial()
    material.color = new THREE.Color(0ff0000)
    material.name = 'material-1'
    material.opacity = 0.5
    material.transparency = true

// 两者差距在于 后者设置颜色必须传入 THREE.Color 对象

2、THREE.MeshBasicMaterial
    一种非常简单的材质，这种材质不考虑场景中光照的影响。使用这种材质的网格会被渲染成简单的平面多边形，而且也可以显示几何体的线框

```

除了上述的共性之外它还能这是这些属性

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/MeshBasicMaterial.png)

```js
var material = new THREE.MeshBasicMaterial({color: 0xff0000})


3、THREE.MeshDepthMaterial
	使用这种材质的物体，其外观不是由光照或某个材质属性决定的，而是由物体到摄像机的距离决定的。可以将这种材质与其他材质结合使用，从而很容易地创建出逐渐消失的效果。
    -wireframe 是否显示线框
    -wireframeLineWidth	指定线框宽度（只对 THREE.CanvasRenderer 有效）
```

#### 融合材质

```js
	看看THREE.MeshDepthMaterial的属性，你会发现没有选项可以用来设置方块的颜色。一切都是由材质的默认属性决定的。但是，Three.js库可以通过联合材质创建出新效果
// 创建几何方块
var cubeSize = Math.ceil(3 + (Math.random() * 3));
var cubeGeometry = new THREE.BoxGeometry(cubeSize, cubeSize, cubeSize);

// 基础材质1
var cubeMaterial = new THREE.MeshDepthMaterial();
// 基础材质2
var colorMaterial = new THREE.MeshBasicMaterial({
    color: controls.color,
    transparent: true,// 不是true就是纯绿色，不会融合
    blending: THREE.MultiplyBlending// 融合模式
});
// 融合至新的方块
var cube = new THREE.SceneUtils.createMultiMaterialObject(cubeGeometry, [colorMaterial,
    cubeMaterial
]);
// 渲染的时候画面会有闪烁,当渲染的物体有一个在别的物体上，并且有一个物体是透明的，这种情况有时会发生。通过缩小带有THREE.MeshDepthMaterial材质的网格，就可以避免这种现象
cube.children[1].scale.set(0.99, 0.99, 0.99);

cube.castShadow = true;
// position the cube randomly in the scene
cube.position.x = -60 + Math.round((Math.random() * 100));
cube.position.y = Math.round((Math.random() * 10));
cube.position.z = -100 + Math.round((Math.random() * 150));

// add the cube to the scene
scene.add(cube);

这些方块从THREE.MeshDepthMaterial对象获得亮度，并从THREE.MeshBasicMaterial获得颜色

4、THREE.MeshNormalMaterial
	法向量在Three.js库中有很广泛的应用。它可以用来决定光的反射，有助于将纹理映射到三维模型，并提供有关如何计算光照、阴影和为表面像素着色的信息

// 我们遍历THREE.SphereGeometry的所有面。对于每个THREE.Face3对象来说，我们通过把构成该面的顶点相加再除以3来计算中心（质心）。使用这个质心连同该面的法向量来绘制箭头。THREE.ArrowHelper对象接受以下参数：direction、origin、length、color、headLength和headWidth。
  for (var f = 0, fl = sphere.geometry.faces.length; f < fl; f++) {
    var face = sphere.geometry.faces[f];
    var centroid = new THREE.Vector3(0, 0, 0);
    centroid.add(sphere.geometry.vertices[face.a]);
    centroid.add(sphere.geometry.vertices[face.b]);
    centroid.add(sphere.geometry.vertices[face.c]);
    centroid.divideScalar(3);

    var arrow = new THREE.ArrowHelper(
      face.normal,
      centroid,
      2,
      0x3333FF,
      0.5,
      0.5);
    // sphere.add(arrow);
  }

5、在单几何体上使用多种材质
    例如，如果你有一个方块，它有12个面（请记住，Three.js只作用于三角形），你可以用这种材质给方块的每个面指定一种材质（例如不同的颜色

var matArray = [];
matArray.push(new THREE.MeshBasicMaterial({color: 0x009e60}))
matArray.push(new THREE.MeshBasicMaterial({color: 0x009e61}))
matArray.push(new THREE.MeshBasicMaterial({color: 0x009e62}))
matArray.push(new THREE.MeshBasicMaterial({color: 0x009e63}))
matArray.push(new THREE.MeshBasicMaterial({color: 0x009e64}))
matArray.push(new THREE.MeshBasicMaterial({color: 0x009e65}))

var cubeGeom = new THREE.BoxGeometry(3,3,3)
var cube = new THREE.Mesh(cubeGeom, matArray)
// 12个面只添加6种材质？
	在Three.js中，几何体的每一个面都具有一个materialIndex属性。该属性指定了该面将使用哪一个具体的材质

cubeGeom.faces.forEach((p,i) => console.log(`face ${i}: ${p.materialIndex}`))
// face 0: 0
// face 1: 0
// face 2: 1
// face 3: 1
// face 4: 2
// face 5: 2
// face 6: 3
// face 7: 3
// face 8: 4
// face 9: 4
// face 10: 5
// face 11: 5



魔方由一些小方块组成：沿x轴3个，沿y轴3个，沿z轴3个。
 // 用来保存所有的方块
  var group = new THREE.Mesh();
  // add all the rubik cube elements
  var mats = [];
  mats.push(new THREE.MeshBasicMaterial({
    color: 0x009e60
  }));
  mats.push(new THREE.MeshBasicMaterial({
    color: 0x0051ba
  }));
  mats.push(new THREE.MeshBasicMaterial({
    color: 0xffd500
  }));
  mats.push(new THREE.MeshBasicMaterial({
    color: 0xff5800
  }));
  mats.push(new THREE.MeshBasicMaterial({
    color: 0xC41E3A
  }));
  mats.push(new THREE.MeshBasicMaterial({
    color: 0xffffff
  }));

  for (var x = 0; x < 3; x++) {
    for (var y = 0; y < 3; y++) {
      for (var z = 0; z < 3; z++) {
        var cubeGeom = new THREE.BoxGeometry(2.9, 2.9, 2.9);
        var cube = new THREE.Mesh(cubeGeom, mats);
        // 设置偏移量
        cube.position.set(x * 3 - 3, y * 3 - 3, z * 3 - 3);

        group.add(cube);
      }
    }
  }



```

#### 高级材质

```js
5、THREE.MeshLambertMaterial
	它既可以模拟塑料质感，也可以在一定程度上模拟金属质感
	这种材质可以用来创建暗淡的并不光亮的表面。该材质非常易用，而且会对场景中的光源产生反应.可以支持线框绘制属性，因此利用它可以绘制具有光照效果的线框物体。
    独有属性
    -color 材质的环境色
    -emissive 材质自发光的颜色。虽然不会是物体成为光源，但会使物体的颜色不受其他光源的影响，该属性默认黑色
    
    
 var meshMaterial = new THREE.MeshLambertMaterial({color: 0x7777ff})
 
 6、THREE.MeshPhongMaterial
	可以创建一种光亮的材质，与上面暗淡材质可使用属性基本一样
    
    -color	材质的环境光。这个颜色会与环境提供的颜色相乘。默认白色
    -emissive	材质自发光
    -specular	指该材质的光亮程度及高光部分的颜色。如果设置与color一样的颜色，将会得到一个更加类似的金属材质，如果设置成灰色（gray）材质将变得像塑料
    -shiness	指定物体表面镜面高光部分的轮廓的清晰程度。越光滑的表面，高光部分越清晰，反之越模糊。默认值 30


var meshMaterial = new THREE.MeshPhongMaterial({color: 0x7777ff})

// Three，js还提供了一个THREE.MeshPhongMaterial材质的扩展材质：THREE.MeshToonMaterial。它的属性与THREE.MeshPhongMaterial完全相同，但是会以不同的方式渲染物体

7、THREE.MeshStandardMaterial
	l这种材质使用更加正确的物理计算来决定物体表面如何与场景中的光源互动。这种材质不但能够更好地表现塑料质感和金属质感的表面
    新属性
    -metalness	控制物体表面的金属程度，0表示完全塑料，1代表完全金属质感。默认值 0.5
	-roughness	控制物体的粗糙程度。在视觉上，他决定表面对入射光的漫反射程度。默认值 0.5.值为0时会产生类似镜面反射，为1时会产生完全的漫反射效果

8、THREE.MeshPhysicalMaterial
	该材质与THREE.MeshStandardMaterial非常相似，但提供了对反光度的更多控制

-clearCoat	该属性控制物体表面清漆图层效果的明显程度。值越高，则图层越厚，其结果是clearCoatRoughness 属性带来的影响越明显。取值范围在0到1之间。默认0

-clearCoatRoughness	控制物体表面清漆图层的粗糙程度，粗糙程度越高漫反射越明显。该属性需要与 clearCoat 属性配合使用，取值0~1.默认0.5

-reflectivity	该属性用于控制金属表面的反光度，因此当matelness（金属感程度） 为1或接近1时该金属的作用很小。取值在0~1之间。默认0.5


9、THREE.ShaderMaterial
	通过它，可以使用自己定制的着色器，直接在WebGL环境中运行。着色器可以将Three.js中的JavaScript网格转换为屏幕上的像素。通过这些自定义的着色器，可以明确地指定对象如何渲染，以及如何覆盖或修改Three.js库中的默认值

	要使用这个材质，必须传入两个不同的着色器：
    vertexShader：它会在几何体的每一个顶点上执行。可以用这个着色器通过改变顶点的位置来对几何体进行变换。
    fragmentShader：它会在几何体的每一个片段上执行。在VertexShader里，我们会返回这个特定片段应该显示的颜色。
```

基础的属性![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/ShaderMaterial.png)

额外的属性![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/ShaderMaterial_ext.png)

#### 线性几何体的材质

```js
10、THREE.Line（线段）
	顾名思义，这只是一条线，线段由顶点组成，不包含任何面。Three.js库提供了两种可用于线段的不同材质，如下所示：
	-THREE.LineBasicMaterial：用于线段的基础材质，可以设置colors、linewidth、linecap和linejoin属性。
	-THREE.LineDashedMaterial：它的属性与THREE.LineBasicMaterial的属性一样，但是可以通过指定虚线和空白间隙的长度来创建出虚线效果。我们将从基础的变体开始，然后再来看看虚线的变体。

11、THREE.LineBasicMaterial

// 通过顶点集合创建一个 THREE.Line 网格，并与 LineMaterial 材质组合以创建网格

//作为获取一些x、y坐标的示例。这个函数返回一个gosper曲线
  var points = gosper(4, 60);

// 创建一个THREE.Geometry实例，为每个坐标创建一个顶点，并把它们放进该实例的lines属性中
  var lines = new THREE.Geometry();
  var colors = [];
  var i = 0;
  points.forEach(function (e) {
    lines.vertices.push(new THREE.Vector3(e.x, e.z, e.y));
    colors[i] = new THREE.Color(0xffffff);
    // setHSL()方法设置颜色。要使用HSL，需提供色调（hue）、饱和度（saturation）和亮度（lightness）
    colors[i].setHSL(e.x / 100 + 0.5, (e.y * 20) / 300, 0.8);
    i++;
  });

  lines.colors = colors;

  var material = new THREE.LineBasicMaterial({
    opacity: 1.0,
    linewidth: 1,
    vertexColors: THREE.VertexColors
  });

  var line = new THREE.Line(lines, material);
  line.position.set(25, -30, -60);
  scene.add(line);
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/LineBasicMaterial.png)

```js
12、THREE.LineDashedMaterial
	这种材质有与THREE.LineBasicMaterial一样的属性，还有几个额外的属性，可以用来定义虚线的宽度和虚线之间的间隙的宽度

-scale	缩放 dashSize 和 gapSize。如果scale 的值小于1，dashSize和gapSize 就会增大；如果scale的值大于1，dashSize 和 gapSize 就会减少
-dashSize	虚线段的长度
-gapSize	虚线间隔的宽度

  var material = new THREE.LineDashedMaterial({
    vertexColors: true,
    color: 0xffffff,
    dashSize: 2,
    gapSize: 2,
    scale: 0.1
  });

  var line = new THREE.Line(lines, material);

// 唯一的区别是必须调用computeLineDistance()（用来计算线段顶点之间的距离）。如果不这么做，间隔就不会正确地显示。
```

### 几何体

```js
1、THREE.CircleGeometry（圆形）
2、THREE.RingGeometry（环形）
3、THREE.PlaneGeometry（平面）
4、THREE.ShapeGeometry（任意图形）
5、THREE.BoxGeometry（长方体）
6、THREE.SphereGeometry（球体）
7、THREE.CylinderGeometry（圆柱体）
8、THREE.ConeGeometry（圆锥体）
9、THREE.TorusGeometry（圆环）
10、THREE.TorusKnotGeometry（环状扭结）
11、THREE.PolyhedronGeometry（多面体）
12、THREE.IcosahedronGeometry（二十面体）
13、THREE.OctahedronGeometry（八面体）
14、THREE.TetraHedronGeometry（四面体）
15、THREE.DodecahedronGeometry（十二面体）
// Three.js中用于代表几何体的两个可用基类：THREE.Geometry 和THREE.BufferGeometry。
	前者是旧版本中所有几何体的基类，而后者则由新版本提供给开发者。新几何体基类THREE.BufferGeometry的内部数据组织形式与GPU所期待的数据结构保持一致，从而进一步提高了运行效率。但是作为代价，新几何体基类的易用性稍差一些。

Geometry:
	-vertices(顶点)	该属性是一个定点数组，储存了用于定义一个几何体的所有顶点的空间位置
    -faces(面)		该属性是一个Face3 数组，其中每一个元素定义了一个面。每个面都通过索引指定了vertices属性中的三个顶点
    
BufferGeometry：
	-attributes(分量)	分量属性所存储的信息直接送往GPU进行处理。比如，要定义一个形体，你需要至少创建一个Float32Array数组，其中每三个元素指定了一个定点的三维空间坐标，而每三个顶点（数组的9个元素）确定一个面。该数组可以如下创建并添加到分量属性中：
	geometry.addAttribute('position', new THREE.BufferAttribute(arrayOfVertices, 3))

	-index(索引)		一般不需要特意指定面，应为默认情况下position（位置）分量中每三个空间坐标确定一个面。但我们也可以通过index属性像 THREE.Geometry 类去指定用于组成每一个面的顶点
    
    
    
// 两种不同形式创建几何体
    1、THREE.Geometry提供了fromBufferGeometry方法，可以接收基于THREE.BufferGeometry的对象，并将其数据导入到THREE.Geometry对象中。
	var normalGemetry = new THREE.Geometry();
	normalGemetry.fromBufferGeometry(bufferGeometry)

    2、THREE.BufferGeometry也提供了fromGeometry方法，用于实现反向转换
	var bufferGeometry = new THREE.BufferGeometry();
	bufferGeometry.fromGeometry(normalGeometry)
```

#### 二维几何体

```js
二维几何体看上去是扁平的，顾名思义，它们只有两个维度。

1、THREE.PlaneGeometry
	创建一个非常简单的二维矩形。
  var test =  new THREE.PlaneGeometry(width, height, widthSegments, heightSegments)
-width	矩形的宽
-height	矩形的高
-widthSegments	指定矩形的宽度应该划分为几段，默认1
-heightSegments	指定矩形的高度应该划分为几段，默认1

// 如果要在几何体创建后访问其属性，要使用parameters属性。test.parameters.width

2、THREE.CircleGeometry
	创建一个非常简单的二维圆（或部分圆）
    -radius	圆的半径决定圆的大小，半径指从圆心到圆弧的距离。默认50
    -segments	该属性定义了创建圆所使用面的数量。最小值为3个，如果没有特别说明，默认值为8.值越大，创建出的圆越光滑
	-thetaStart	该属性定义了从哪里开始画圆。范围0-2PI。默认值0
    -thetaLength	该属性定义圆要画多大。如果没有指定，默认值为2*PI（整圆）

// 画整圆
	new THREE.CircleGeometry(3,12)
// 画个半圆
	new THREE.CircleGeometry(3, 12, 0, Math.PI)


	Three.js创建的这些对象都是“直立”的，所以它们只位于x-y平面.有时我们希望这个网格“躺”下来，以便构建一种地面（x-z平面），好把其他对象放在上面。创面一个水平放置而不是竖直的二维对象，最简单的方法是将网格沿x轴向后旋转四分之一圈
    mesh.rotation.x = -Math.PI/2

3、THREE.RingGeometry
	类似于圆环，可以在中心定义一个孔
	var ring = new THREE.RingGeometry();
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/RingGeometry.png)

```js
4、THREE.ShapeGeometry
	自定义二维图形，与 svg的 path 元素功能类似
	
function drawShape() {

  // create a basic shape
  var shape = new THREE.Shape();

  // startpoint
  shape.moveTo(10, 10);

  // straight line upwards
  shape.lineTo(10, 40);

  // the top of the figure, curve to the right
  shape.bezierCurveTo(15, 25, 25, 25, 30, 40);

  // spline back down
  shape.splineThru(
    [new THREE.Vector2(32, 30),
      new THREE.Vector2(28, 20),
      new THREE.Vector2(30, 10),
    ]);

  // curve at the bottom
  shape.quadraticCurveTo(20, 15, 10, 10);

  // add 'eye' hole one
  var hole1 = new THREE.Path();
  hole1.absellipse(16, 24, 2, 3, 0, Math.PI * 2, true);
  shape.holes.push(hole1);

  // add 'eye hole 2'
  var hole2 = new THREE.Path();
  hole2.absellipse(23, 24, 2, 3, 0, Math.PI * 2, true);
  shape.holes.push(hole2);

  // add 'mouth'
  var hole3 = new THREE.Path();
  hole3.absarc(20, 16, 2, 0, Math.PI, true);
  shape.holes.push(hole3);

  // return the shape
  return shape;
}
// 这个函数的调用结果是一个用来创建网格的几何体
new THREE.ShapeGeometry(drawShape())

// 当你已经有一个图形时，还有一种方式创建THREE.ShapeGeometry对象。可以调用shape.makeGeometry(options)，这个函数会返回一个THREE.ShapeGeometry的实例

```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/ShapeGeometry.png)

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/ShapeGeometry_ext.png)

#### 三维几何体

```js
1、THREE.BoxGeometry
	一种非常简单的三维几何体，只需要指定宽度、高度和深度就可以创建一个长方体
    new THREE.BoxGeometry(10,10,10)
-width	宽，沿x轴方向的长度
-height	高，沿y轴方向的长度
-depth	深度，沿z轴方向的长度
-widthSegments	沿x轴将面分成多少份
-heightSegments	沿y轴将面分成多少份
-depthSegments	沿z轴将面分成多少份

2、THREE.SphereGeometry
	创建一个三维球体

    var baseSphere = new THREE.SphereGeometry(4, 10, 10);
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/SphereGeometry.png)

```js
3、THREE.CylinderGeometry
	创建圆柱和类似圆柱的物体
    
new THREE.CylinderGeometry() //即可创建一个圆柱
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/CylinderGeometry.png)

```js
4、THREE.ConeGeometry
	它几乎拥有THREE.CylinderGeometry 的所有属性，唯一的区别是它不支持独立的radiusTop（顶半径）和radiusBottom（底半径），而是只有一个作为底半径的radius属性。

```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/ConeGeometry.png)

```js
5、THREE.TorusGeometry
	Torus（圆环）是一种看起来像甜甜圈的简单图形
    
    其中的arc属性是一个非常有趣的属性。通过这个属性，你可以指定是绘制一个完整的圆环还是部分圆环。通过试验这个属性，你可以创建一些非常有趣的网格
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/TorusGeometry.png)

```js
6、THREE.TorusKnotGeometry
	可以创建一个环状扭结。环状扭结是一种比较特别的结，看起来就像一根管子绕自己转了几圈
    
    修改属性p和q，就可以创建各种各样漂亮的几何体。p属性定义扭结绕其轴线旋转的频率，q属性定义扭结绕其内部缠绕多少次
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/TorusKnotGeometry.png)

```js
7、THREE.PolyhedronGeometry
	使用这个几何体，可以很容易地创建多面体。多面体是只有平面和直边的几何体
    
     var vertices = [
      1, 1, 1, -1, -1, 1, -1, 1, -1, 1, -1, -1
    ];

    var indices = [
      2, 1, 0, 0, 3, 2, 1, 3, 0, 2, 3, 1
    ];
    polyhedron = new THREE.PolyhedronGeometry(vertices, indices, controls.radius, controls.detail);
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/PolyhedronGeometry.png)

```js
8、THREE.IcosahedronGeometry
	可以创建出一个有20个相同三角形面的多面体，这些三角形面是从12个顶点创建出来的。创建这个多面体时，你要做的只是指定radius和detail的值
    
9、THREE.TetrahedronGeometry
	（正四面体）是最简单的多面体。这个多面体只包含由4个顶点创建的4个三角形面。创建THREE.TetrahedronGeometry跟创建Three.js提供的其他多面体一样，只要指定radius和detail的值。

10、THREE.OctahedronGeometry
	这个多面体有8个面。这些面是从6个顶点创建出来的
11、THREE.DodecahedronGeometry
	这个多面体有12个面
    
    
    
// 小结
    一种好的方法是：开始创建几何体时，可以选择一种简单的材质，不要直接使用那些复杂的材质，可以从简单的THREE.MeshBasicMaterial材质开始，并将wireframe属性设为true，或者使用THREE.MeshNormal-Material材质也可以。这样，你就可以对几何体的真实形状有一个更好的了解。对于二维图形，重要的是要记住它们是放置在x-y平面上的。如果你想拥有一个水平的二维图形，那么你必须将这个网格绕x轴旋转-0.5*PI。最后，如果你要旋转一个二维图形，或者一个开放的三维图形（例如圆柱或管），记住要将材质设置成THREE.DoubleSide。如果你不这么做，那么该几何体的内侧或背面将会不可见。
```

### 高级几何体及二元操作

```js
1、THREE.ConvexGeometry
	可以围绕一组点创建一个凸包。所谓凸包就是包围这组点的最小图形
    需要额外引入 ConvexGeometry.js

  var spGroup;
  function generatePoints() {

    if (spGroup) scene.remove(spGroup)
    // add 10 random spheres
    var points = [];
    for (var i = 0; i < 20; i++) {
      var randomX = -15 + Math.round(Math.random() * 30);
      var randomY = -15 + Math.round(Math.random() * 30);
      var randomZ = -15 + Math.round(Math.random() * 30);

      points.push(new THREE.Vector3(randomX, randomY, randomZ));
    }

    spGroup = new THREE.Object3D();
    var material = new THREE.MeshBasicMaterial({
      color: 0xff0000,
      transparent: false
    });
    points.forEach(function (point) {

      var spGeom = new THREE.SphereGeometry(0.2);
      var spMesh = new THREE.Mesh(spGeom, material);
      spMesh.position.copy(point);
      spGroup.add(spMesh);
    });
    // add the points as a group to the scene
    scene.add(spGroup);

    // use the same points to create a convexgeometry
    // THREE.ConvexGeometry的构造函数接收一个顶点数组作为唯一参数
    var convexGeometry = new THREE.ConvexGeometry(points);
    // 我们明确地调用了computeVertexNormals和computeFaceNormals两个函数来计算法线，这是因为顶点和面的法线是Three.js为物体渲染平滑的表面所必需的数据。虽然大部分几何体在创建对象时会自动计算这些法线，但是只有这个类的对象是个例外
    convexGeometry.computeVertexNormals();
    convexGeometry.computeFaceNormals();
    convexGeometry.normalsNeedUpdate = true;
    return convexGeometry;
  }

2、THREE.LatheGeometry
	允许你从一条光滑曲线创建图形。类似与将一条线绕某个点旋转得到一个物体
    
    
  function generatePoints(segments, phiStart, phiLength) {

    if (spGroup) scene.remove(spGroup)

    var points = [];
    var height = 5;
    var count = 30;
    for (var i = 0; i < count; i++) {
      points.push(new THREE.Vector2((Math.sin(i * 0.2) + Math.cos(i * 0.3)) * height + 12, (i - count) + count / 2));
    }

    spGroup = new THREE.Object3D();
    var material = new THREE.MeshBasicMaterial({
      color: 0xff0000,
      transparent: false
    });
    points.forEach(function (point) {

      var spGeom = new THREE.SphereGeometry(0.2);
      var spMesh = new THREE.Mesh(spGeom, material);
      spMesh.position.set(point.x, point.y, 0);

      spGroup.add(spMesh);
    });
    // add the points as a group to the scene
    scene.add(spGroup);

    // use the same points to create a LatheGeometry
    var latheGeometry = new THREE.LatheGeometry(points, segments, phiStart, phiLength);
    return latheGeometry;
  }
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/LatheGeometry.png)

#### 通过拉伸创建几何体

```js
	Three.js提供了几种方法，让我们可以从一个二维图形拉伸出三维图形。拉伸指的是沿着z轴拉伸二维图形，将它转换成三维图形。例如，如果我们拉伸THREE.CircleGeometry，就会得到一个类似圆柱体的图形；如果我们拉伸THREE.PlaneGeometry，就会得到一个类似方块的图形。

1、THREE.ExtrudeGeometry
	可以从一个二维图形创建出一个三维图形
    
    //明确定义图形应该怎样拉伸
    var options = {
      amount: controls.amount,
      bevelThickness: controls.bevelThickness,
      bevelSize: controls.bevelSize,
      bevelSegments: controls.bevelSegments,
      bevelEnabled: controls.bevelEnabled,
      curveSegments: controls.curveSegments,
      steps: controls.steps
    };

    var geom = new THREE.ExtrudeGeometry(drawShape(), options);
    geom.applyMatrix(new THREE.Matrix4().makeTranslation(-20, 0, 0));
    geom.applyMatrix(new THREE.Matrix4().makeScale(0.4,0.4,0.4));
	// drawShap()函数创建了一个图形
	function drawShape() {

        // create a basic shape
        var shape = new THREE.Shape();

        // startpoint
        shape.moveTo(10, 10);

        // straight line upwards
        shape.lineTo(10, 40);

        // the top of the figure, curve to the right
        shape.bezierCurveTo(15, 25, 25, 25, 30, 40);

        // spline back down
        shape.splineThru(
          [new THREE.Vector2(32, 30),
            new THREE.Vector2(28, 20),
            new THREE.Vector2(30, 10),
          ]);

        // curve at the bottom
        shape.quadraticCurveTo(20, 15, 10, 10);

        // add 'eye' hole one
        var hole1 = new THREE.Path();
        hole1.absellipse(16, 24, 2, 3, 0, Math.PI * 2, true);
        shape.holes.push(hole1);

        // add 'eye hole 2'
        var hole2 = new THREE.Path();
        hole2.absellipse(23, 24, 2, 3, 0, Math.PI * 2, true);
        shape.holes.push(hole2);

        // add 'mouth'
        var hole3 = new THREE.Path();
        hole3.absarc(20, 16, 2, 0, Math.PI, true);
        shape.holes.push(hole3);

        // return the shape
        return shape;
    }
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/ExtrudeGeometry.png)

```js
2、THREE.TubeGeometry
	沿着一条三维的样条曲线拉伸出一根管
// 获取一组THREE.Vector3类型的顶点
  var points = [];
  for (var i = 0; i < controls.numberOfPoints; i++) {
    var randomX = -20 + Math.round(Math.random() * 50);
    var randomY = -15 + Math.round(Math.random() * 40);
    var randomZ = -20 + Math.round(Math.random() * 40);

    points.push(new THREE.Vector3(randomX, randomY, randomZ));
  }
// 将这些点转换成THREE.CatmullRomCurve3对象
var tubeGeometry =	new THREE.TubeGeometry(new THREE.CatmullRomCurve3(points), segments, radius, radiusSegments, closed);
// 
var tubeMesh = createMesh(tubeGeometry)
scene.add(tubeMesh)
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/TubeGeometry.png)

#### 从SVG拉伸

```js
SVG跟Three.js处理图形的方式非常一致
    d3-threeD库，可以自动转换这些指令。将svg path 转换成指令
	https://github.com/asutherland/d3-threeD 的小型库SVG路径转换成Three.js图形。
	
	直接使用SVG，通过JavaScript来操作它并不直观。幸运的是，有几个开源JavaScript库使得使用SVG更简单。Paper.js、Snap.js、D3.js和Raphael.js是其中最好的几个

SVG图：源码
	<svg svg version="1.0" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" width="1152px"
            height="1152px" xml:space="preserve">
            <g>
                <path id="batman-path" style="fill:rgb(0,0,0);" d="M 261.135 114.535 C 254.906 116.662 247.491 118.825 244.659 119.344 C 229.433 122.131 177.907 142.565 151.973 156.101 C 111.417 177.269 78.9808 203.399 49.2992 238.815 C 41.0479 248.66 26.5057 277.248 21.0148 294.418 C 14.873 313.624 15.3588 357.341 21.9304 376.806 C 29.244 398.469 39.6107 416.935 52.0865 430.524 C 58.2431 437.23 63.3085 443.321 63.3431 444.06 C 63.4748 446.883 102.278 479.707 120.51 492.418 C 131.003 499.734 148.168 509.93 158.654 515.075 C 169.139 520.22 179.431 525.34 181.524 526.454 C 187.725 529.754 187.304 527.547 179.472 515.713 C 164.806 493.553 158.448 464.659 164.322 446.861 C 169.457 431.303 192.013 421.501 214.324 425.132 C 234.042 428.341 252.142 439.186 270.958 459.064 C 286.677 475.67 292.133 482.967 295.31 491.634 C 297.466 497.514 298.948 495.91 304.862 481.293 C 313.673 459.519 329.808 445.735 346.35 445.851 C 367.654 446 399.679 478.239 412.801 512.745 C 414.093 516.144 416.593 522.632 418.355 527.163 C 420.118 531.695 423.604 542.319 426.103 550.773 C 430.848 566.832 432.355 566.851 434.872 550.88 C 436.395 541.215 451.403 502.522 455.655 497.298 C 457.038 495.599 460.63 489.896 463.636 484.625 C 471.696 470.498 492.318 452.688 505.387 448.568 C 514.602 445.663 517.533 445.549 525.51 447.782 C 539.676 451.749 553.43 467.773 560.706 488.788 L 563.242 496.114 L 567.096 490.012 C 577.709 473.208 593.665 453.899 602.47 447.206 C 607.884 443.09 613.378 438.825 614.679 437.729 C 615.98 436.632 622.927 433.259 630.118 430.233 C 655.159 419.693 681.195 423.407 693.273 439.241 C 697.957 445.382 698.932 448.971 699.538 462.294 C 700.174 476.284 699.51 479.864 693.686 493.854 C 690.073 502.533 684.912 512.883 682.217 516.854 C 679.523 520.825 678.172 524.074 679.215 524.074 C 681.932 524.074 718.787 504.481 732.525 495.734 C 760.018 478.228 788.909 452.599 803.9 432.418 C 807.266 427.886 810.569 423.715 811.239 423.149 C 814.498 420.395 828.253 393.099 833.17 379.627 C 838.223 365.782 838.713 361.822 838.741 334.582 C 838.776 300.425 836.431 291.124 820.154 260.873 C 810.649 243.207 807.498 239.005 788.417 218.543 C 751.511 178.968 688.147 142.549 621.582 122.654 C 581.7 110.734 580.388 110.465 580.388 114.195 C 580.388 115.328 581.302 116.255 582.418 116.255 C 584.279 116.255 587.705 122.106 603.399 152.085 C 613.977 172.29 618.077 189.427 618.264 214.21 C 618.42 234.928 617.88 238.368 612.285 252.269 C 604.327 272.04 590.066 286.889 572.829 293.352 C 558.526 298.714 549.193 297.86 535.704 289.955 C 526.777 284.723 512.304 267.644 509.816 259.404 C 509.132 257.138 507.129 251.358 505.366 246.558 C 503.602 241.759 501.646 231.564 501.018 223.902 C 500.39 216.24 498.491 198.402 496.797 184.261 C 495.104 170.121 493.307 152.047 492.803 144.097 C 492.299 136.147 491.292 125.625 490.565 120.715 L 489.242 111.787 L 483.323 118.267 C 480.067 121.832 477.404 125.618 477.404 126.681 C 477.404 127.744 476.603 128.613 475.624 128.613 C 474.645 128.613 471.275 132.321 468.135 136.852 L 462.426 145.091 L 431.038 145.091 L 399.65 145.091 L 386.811 128.494 C 379.749 119.365 373.509 112.36 372.943 112.926 C 372.377 113.491 371.57 118.875 371.15 124.888 C 370.73 130.902 368.94 147.744 367.172 162.315 C 365.405 176.887 363.523 195.424 362.99 203.509 C 360.283 244.622 352.784 266.044 335.323 282.544 C 326.456 290.923 312.488 297.497 303.508 297.518 C 294.864 297.539 278.732 290.063 269.473 281.748 C 246.952 261.521 238.846 229.614 245.481 187.314 C 247.894 171.928 266.562 131.612 275.927 121.56 C 277.987 119.348 279.673 116.786 279.673 115.867 C 279.673 114.947 279.905 113.593 280.188 112.856 C 281.28 110.017 271.977 110.837 261.136 114.536 L 261.135 114.535 "
                />
            </g>

        </svg>
// 额外引用了 d3-threeD.js

var options = {
  amount: controls.amount,
  bevelThickness: controls.bevelThickness,
  bevelSize: controls.bevelSize,
  bevelSegments: controls.bevelSegments,
  bevelEnabled: controls.bevelEnabled,
  curveSegments: controls.curveSegments,
  steps: controls.steps
};
function drawShape() {
    var svgString = document.querySelector("#batman-path").getAttribute("d");
    var shape = transformSVGPathExposed(svgString);
    // return the shape
    return shape;
}
var geom = new THREE.ExtrudeGeometry(drawShape(), options)
geom.applyMatrix(new THREE.Matrix4().makeScale(0.05,0.05,0.05));
geom.center();


// 你会看到有一个transformSVGPathExposed函数被调用。这个函数是由d3-threeD库提供的，接受一个SVG字符串作为参数。 SVG元素的d属性包含的就是用来绘制图形的路径表达式

```

```js
3、THREE.ParametricGeometry
	可以创建基于等式的几何体
	当你下载Three.js发布包后，你会得到examples/js/ParametricGeometries.js文件。在这个文件中，你可以找到几个公式的例子，它们可以和THREE.ParametricGeometry一起使用。
	
    radialWave = function (u, v, optionalTarget) {

        var result = optionalTarget || new THREE.Vector3();
        var r = 50;

        var x = Math.sin(u) * r;
        var z = Math.sin(v / 2) * 2 * r;
        var y = (Math.sin(u * 4 * Math.PI) + Math.cos(v * 2 * Math.PI)) * 2.8;

        return result.set( x, y, z );
    };
    var mesh  = createMesh(new THREE.ParamentricGeometry(radialWave, 120, 120, false))

THREE.ParametricGeometry参数
-function	该属性是一个函数，该函数以u,v值作为参数来定义每个顶点位置
-slices		该属性定义u值应该多少份
-stacks		该属性定义v值应该分成多少份

// 例如，如果将slices属性设置为5并且将stacks属性设为4，那么在调用函数时将使用以下参数值
u: 0/5	v:0/4
u: 1/5	v:0/4
u: 2/5	v:0/4
u: 3/5	v:0/4
u: 4/5	v:0/4
u: 5/5	v:0/4
u: 0/5	v:1/4
u: 1/5	v:1/4
...
u: 5/5	v:3/4
u: 6/5	v:4/4
```

#### 三维文本

```js
var loadFont;
var fontload = new THREE.FontLoader();
fontload.load( '../../assets/fonts/bitstream_vera_sans_mono_roman.typeface.json', function ( response ) {
    controls.font = response;
    font_bitstream = response;
    render();
  });

var options = {
  size: controls.size,
  height: controls.height,
  weight: controls.weight,
  font: controls.font,
  bevelThickness: controls.bevelThickness,
  bevelSize: controls.bevelSize,
  bevelSegments: controls.bevelSegments,
  bevelEnabled: controls.bevelEnabled,
  curveSegments: controls.curveSegments,
  steps: controls.steps
};

var geom = new THREE.TextGeometry("Learning Three.js", options)
let text = createMesh(geom)
scene.add(text)

```

#### ![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/TextGeometry.png)自定义字体

```js
如果你想渲染二维文字并用作材质的纹理，那么你就不应该使用THREE.TextGeometry

	Three.js提供了几种可以在场景中使用的字体。这些字体基于由TypeFace.js库提供的字体。TypeFace.js库可以将TrueType和OpenType字体转换为JavaScript文件或者JSON文件，以便在网页中的JavaScript程序中直接使用。在旧版本的Three.js使用字体时，需要用转换得到的JavaScript文件，而新版Three.js改为使用JSON文件了。

转换得到JSON文件后，你可以使用THREE.FontLoader（就像我们在前面展示过的那样）加载字体，并将字体对象赋给THREE.TextGeometry的font属性。


```

#### 构造实体几何体（Constructive Solid Geometry，CSG）的技术

```js
ThreeBSP库，该库使用二元操作创建出相交、相减和联合等非常有趣的几何体。
	为此，我们引入了Three.js的扩展库ThreeBSP	https://github.com/skalnik/ThreeBSP

它提供了三个函数
-intersect(相交)
	基于两个现有几何体的交集创建出来新的几何体。两个几何体重叠的部分定义此新的几何体形状
    
-union(联合)
	将两个几何体联合起来创建一个新的几何体
    
-subtract(相减)

	可以在第一个几何体中移出两个几何体重叠的部分来创建新的几何体

    
// 要使用这个库，需要把它包含在我们的页面中。这个库是用CoffeeScript写的，这是一种对用户更加友好的JavaScript脚本的变体.	
// 要使这个库可以正常工作，我们有两个选择：
    1、添加CoffeeScript文件，并在运行时编译；
    	-引入coffee-script.js
		-onload 之后再执行代码，确保代码已经编译完成
    2、或者将它预先编译成JavaScript文件，然后直接包含它
    	-安装CoffeeScript
        -coffee --compile ThreeBSP.coffee// ThreeBSP.coffee -> ThreeBSP.js

//[注意]：
	这三个函数在计算时使用的是网格的绝对位置，所以如果你在应用这些函数之前将网格组合在一起或是使用多种材质，你可能会得到一些奇怪的结果。为了得到最好的、可预测的结果，应当确保使用的是未经组合的网格。
```

### 粒子和精灵

```js
	粒子和精灵。使用THREE.Points（有时也叫作精灵（sprite）），可以非常容易地创建很多细小的物体，用来模拟雨滴、雪花、烟和其他有趣的效果。例如，你可以将整个几何体渲染成一组粒子，并分别控制它们。
	// Three.js的最新版本更新了与粒子相关的对象的名称。THREE.Points以前称为THREE.PointCloud，在更早以前曾被称为THREE.ParticleSystem。THREE.Sprite以前称为THREE.Particle，而且还更改了一些材质的名称。

    // 跟THREE.Mesh一样，THREE.Sprite对象也是THREE.Object3D对象的扩展。也就是说THREE.Mesh的大部分属性和函数都可以用于THREE.Sprite。


  function createSprites() {

    for (var x = -15; x < 15; x++) {
      for (var y = -10; y < 10; y++) {
        // 创建粒子材质只能是 SpriteMaterial / SpriteCanvasMaterial
        var material = new THREE.SpriteMaterial({
          color: Math.random() * 0xffffff
        });

        var sprite = new THREE.Sprite(material);
        sprite.position.set(x * 4, y * 4, 0);
        scene.add(sprite);
      }
    }
  }
// 优化后
function createSprites() {
    var geom = new THREE.Geometry()
    var material = new THREE.PointsMasterial({
        size: 2,
        vertexColors: true,
        color: 0xffffff
    })
    for (var x = -15; x < 15; x++) {
      for (var y = -10; y < 10; y++) {
        var particle = new THREE.Vector3(x*4, y*4, 0)
        geom.vertices.push(particle)
        geom.colors.push(new THREE.Color(Math.random() * 0xffffff))
      }
    }
    // THREE.Points(几何体,材质)
    var cloud = new THREE.Points(geom, material)
    scene.add(cloud)
  }


我们只是把粒子渲染为小方块，这是默认行为。但是，还有其他的一些方式可以用来样式化粒子：我们可以应用
    -THREE.SpriteCanvasMaterial（只适应于THREE.CanvasRenderer），将HTML画布元素绘制的内容作为粒子的纹理。
    在使用THREE.WebGLRenderer时，可以使用THREE.SpriteMaterial渲染HTML画布的元素。
	-使用THREE.PointsMaterial的map属性，加载外部图片文件（或使用HTML5画布）来样式化THREE.Points对象中的所有粒子。
```

#### ![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/PointsMaterial.png)使用HTML5画布样式化粒子

```js
Three.js提供了三种使用HTML5画布来样式化粒子的方法
1、 在THREE.CanvasRenderer中使用HTML5画布
	
  function createSprites() {
    var material = new THREE.SpriteCanvasMaterial({
      program: getTexture
    });

    material.rotation = Math.PI;

    var range = 500;
    for (var i = 0; i < 1500; i++) {
      var sprite = new THREE.Sprite(material);
      sprite.position.set(Math.random() * range - range / 2, Math.random() * range - range / 2, Math.random() *
        range - range / 2);
      sprite.scale.set(0.1, 0.1, 0.1);
      scene.add(sprite);
    }
  }
// getTexture函数定义了粒子的外观, 接受二维画布上下文（ctx）作为其参数
var getTexture = function (ctx) {

    // the body
    ctx.translate(-81, -84);

    ctx.fillStyle = "orange";
    ctx.beginPath();
    ctx.moveTo(83, 116);
    ctx.lineTo(83, 102);
    ctx.bezierCurveTo(83, 94, 89, 88, 97, 88);
    ctx.bezierCurveTo(105, 88, 111, 94, 111, 102);
    ctx.lineTo(111, 116);
    ctx.lineTo(106.333, 111.333);
    ctx.lineTo(101.666, 116);
    ctx.lineTo(97, 111.333);
    ctx.lineTo(92.333, 116);
    ctx.lineTo(87.666, 111.333);
    ctx.lineTo(83, 116);
    ctx.fill();

    // the eyes
    ctx.fillStyle = "white";
    ctx.beginPath();
    ctx.moveTo(91, 96);
    ctx.bezierCurveTo(88, 96, 87, 99, 87, 101);
    ctx.bezierCurveTo(87, 103, 88, 106, 91, 106);
    ctx.bezierCurveTo(94, 106, 95, 103, 95, 101);
    ctx.bezierCurveTo(95, 99, 94, 96, 91, 96);
    ctx.moveTo(103, 96);
    ctx.bezierCurveTo(100, 96, 99, 99, 99, 101);
    ctx.bezierCurveTo(99, 103, 100, 106, 103, 106);
    ctx.bezierCurveTo(106, 106, 107, 103, 107, 101);
    ctx.bezierCurveTo(107, 99, 106, 96, 103, 96);
    ctx.fill();

    // the pupils
    ctx.fillStyle = "blue";
    ctx.beginPath();
    ctx.arc(101, 102, 2, 0, Math.PI * 2, true);
    ctx.fill();
    ctx.beginPath();
    ctx.arc(89, 102, 2, 0, Math.PI * 2, true);
    ctx.fill();

  };

2、 在WebGLRenderer中使用HTML5画布
	-可以使用THREE.PointsMaterial并创建THREE.Points对象
	// 将这个纹理赋给THREE.PointsMaterial的map属性
    function createPoints(size, transparent, opacity, sizeAttenuation, color) {

        var geom = new THREE.Geometry();

        var material = new THREE.PointsMaterial({
          size: size,
          transparent: transparent,
          opacity: opacity,
          map: createGhostTexture(),
          sizeAttenuation: sizeAttenuation,
          color: color
        });

        var range = 500;
        for (var i = 0; i < 5000; i++) {
          var particle = new THREE.Vector3(Math.random() * range - range / 2, Math.random() * range - range / 2,
            Math.random() * range - range / 2);
          geom.vertices.push(particle);
        }

        cloud = new THREE.Points(geom, material);
        cloud.name = 'points';
        scene.add(cloud);
      }
	-可以使用THREE.Sprite和THREE.SpriteMaterial的map属性。
	//
      function createSprites() {
        var material = new THREE.SpriteMaterial({
          map: createGhostTexture(),
          color: 0xffffff
        });

        var range = 500;
        for (var i = 0; i < 1500; i++) {
          var sprite = new THREE.Sprite(material);
          sprite.position.set(Math.random() * range - range / 2, Math.random() * range - range / 2, Math.random() *
            range - range / 2);
          sprite.scale.set(4, 4, 4);
          scene.add(sprite);
        }
      }

// 基于HTML5画布元素创建了一个THREE.Texture对象
function createGhostTexture() {
    var canvas = document.createElement('canvas');
    canvas.width = 32;
    canvas.height = 32;

    var ctx = canvas.getContext('2d');
    // the body
    ctx.translate(-81, -84);
    ...

    // the eyes
    ...
    // the pupils
    ...


    var texture = new THREE.Texture(canvas);
    texture.needsUpdate = true;
    return texture;
};
```

THREE.SpriteCanvasMaterial 这种材质上的属性

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/SpriteCanvasMaterial.png)

#### 使用纹理样式化粒子

```js
THREE.TextureLoader().load()
// 数将图像加载为THREE.Texture对象，然后就可以将THREE.Texture分配给材质的map属性

// 将外部图片加载为 THREE.Texture对象
var texture = new THREE.TextureLoader().load("../../assets/textures/particles/raindrop-3.png");

// eg:
function createPointCloud(size, transparent, opacity, sizeAttenuation, color) {


    var texture = new THREE.TextureLoader().load("../../assets/textures/particles/raindrop-3.png");
    var geom = new THREE.Geometry();

    var material = new THREE.PointsMaterial({
      size: size,
      transparent: transparent,
      opacity: opacity,
      map: texture,
      depthWrite: false,// 将它设置为false，可以保证在各个的位置上渲染的雪花之间不会互相影响.否则一个粒子在另一个THREE.Points对象的粒子前面时，你可能会看到纹理的黑色背景
      blending: THREE.AdditiveBlending,
      sizeAttenuation: sizeAttenuation,
      color: color
    });


    var range = 40;
    for (var i = 0; i < 1500; i++) {
      var particle = new THREE.Vector3(
        Math.random() * range - range / 2,
        Math.random() * range * 1.5,
        // Math.random() * range - range / 2
        1 + (i/100)
      )
      particle.velocityY = 0.1 + Math.random() / 5;
      particle.velocityX = (Math.random() - 0.5) / 3;
      geom.vertices.push(particle);
    }

    cloud = new THREE.Points(geom, material);
    cloud.sortParticles = true;
    cloud.name = "particles1"

    scene.add(cloud);
  }
```

#### 使用精灵贴图

```js
精灵贴图中包含多个图像，通过移动缩放显示精灵贴纸来改变图像内容
function createSprite(size, transparent, opacity, color, spriteNumber) {
    var spriteMaterial = new THREE.SpriteMaterial({
      opacity: opacity,
      color: color,
      transparent: transparent,
      map: getTexture()
    });

    // we have 1 row, with five sprites
    spriteMaterial.map.offset = new THREE.Vector2(0.2 * spriteNumber, 0);
    spriteMaterial.map.repeat = new THREE.Vector2(1 / 5, 1);
    spriteMaterial.blending = THREE.AdditiveBlending;
    // make sure the object is always rendered at the front
    spriteMaterial.depthTest = false;
    
    var sprite = new THREE.Sprite(spriteMaterial);
    sprite.scale.set(size, size, size);
    sprite.position.set(100, 50, -10);
    sprite.velocityX = 5;

    sceneOrtho.add(sprite);
  }

  var getTexture = function () {
    var texture = new THREE.TextureLoader().load("../../assets/textures/particles/sprite-sheet.png");
    return texture;
  };
```

### 加载高级网格和几何体

#### 从高级几何体创建THREE.Points

```js
THREE.Points基于几何体的顶点来渲染每个粒子.我们就可以基于这个几何体的顶点创建出一个THREE.Points对象

// 它将这个网格转换为THREE.Points

//我们直接从指定的几何体（在本例中是一个环状扭结）创建了一个简单的THREE.Points对象
function generateSprite() {

    var canvas = document.createElement('canvas');
    canvas.width = 16;
    canvas.height = 16;

    var context = canvas.getContext('2d');
    var gradient = context.createRadialGradient(canvas.width / 2, canvas.height / 2, 0, canvas.width / 2, canvas.height / 2, canvas.width / 2);
    gradient.addColorStop(0, 'rgba(255,255,255,1)');
    gradient.addColorStop(0.2, 'rgba(0,255,255,1)');
    gradient.addColorStop(0.4, 'rgba(0,0,64,1)');
    gradient.addColorStop(1, 'rgba(0,0,0,1)');

    context.fillStyle = gradient;
    context.fillRect(0, 0, canvas.width, canvas.height);

    var texture = new THREE.Texture(canvas);
    texture.needsUpdate = true;
    return texture;

  }
// 通过generateSprite()函数将粒子的纹理（map属性）设置成发光点
 function createPoints(geom) {
    var material = new THREE.PointsMaterial({
      color: 0xffffff,
      size: 3,
      transparent: true,
      blending: THREE.AdditiveBlending,
      map: generateSprite(),
      depthWrite: false // instead of sortParticles
    });

    var cloud = new THREE.Points(geom, material);
    return cloud;
  }
```

#### 创建、加载高级网格和几何体

```js
1、组合和合并。
	-将对象组合在一起，以及将多个网格合并为一个网格
	// 操作组会绕其中心影响组中的每个对象，例如缩放、移动、旋转。。。所有的定位、旋转和变形都是相对父对象的
	sphere = createMesh(new THREE.SphereGeometry(5, 10, 10))
    cube = createMesh(new THREE.BoxGeometry(6, 6, 6))
	group = new THREE.Group()
	group.add(sphere)
	group.add(cube)

	// 将多个网格合并成一个网格
	场景中能够添加的网格数量有一个上限
    还提供该对象的变换矩阵。当我们将此矩阵添加到merge函数后，那么合并的方块将会被正确定位。
    THREE.Geometry.merge()
2、从外部加载。
	-加载保存 THREE.Mesh
	 var knot = createMesh(new THREE.TorusKnotGeometry(10, 1, 64, 8, 2, 3));
	// 保存
      var result = knot.toJSON();
      localStorage.setItem("json", JSON.stringify(result));
      console.log(localStorage.getItem("json"));

	//加载
      scene.remove(loadedMesh);

      var json = localStorage.getItem("json");

      if (json) {
        var loadedGeometry = JSON.parse(json);
        // 使用它可以将JSON转换成THREE.Mesh对象.我们使用的是加载器的parse方法来直接解析JSON字符串。该加载器还提供了一个load函数，你可以传入一个URL地址，该地址指向一个含有JSON定义的文件。
        var loader = new THREE.ObjectLoader();

        loadedMesh = loader.parse(loadedGeometry);
        loadedMesh.position.x -= 40;
        scene.add(loadedMesh);
      }
	// 如果你想要保存整个场景，包括光源和摄像机，可以使用THREE.SceneExporter。
	-保存加载场景
	// 保存
	localStorage.setItem('scene', JSON.stringify(scene.toJSON()));
	// 加载
	 var json = (localStorage.getItem('scene'));

      if (json) {
        var loadedSceneAsJson = JSON.parse(json);
        var loader = new THREE.ObjectLoader();
        scene = loader.parse(loadedSceneAsJson);
      }

```

支持一下外部格式资源

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/externalResources1.png)

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/externalResources2.png)

#### Blender

```js
	有很多三维软件可以用来创建复杂的网格。其中有一个流行的开源软件叫作Blender（www.blender.org）。Three.js库有一个Blender（以及Maya和3D Studio Max）导出器，可以直接将文件导出为Three.js的JSON格式
	
// setup the scene for rendering
var camera = initCamera(new THREE.Vector3(50, 50, 50));
var loaderScene = new BaseLoaderScene(camera);
camera.lookAt(new THREE.Vector3(0, 15, 0));

var loader = new THREE.JSONLoader();
// geometry参数包含模型对象，而mat参数包含一组材质对象
loader.load('../../assets/models/house/house.json', function (geometry, mat) {

    var mesh = new THREE.Mesh(geometry, mat[0]);
    mesh.castShadow = true;
    mesh.receiveShadow = true;

    // call the default render loop.
    loaderScene.render(mesh, camera);
});
```

#### 导入三维格式文件

```js
 1、OBJ和MTL格式
 
	 OBJ和MTL是相互配合的两种格式，经常一起使用。OBJ文件定义几何体，而MTL文件定义所用的材质
     需要引入依赖 MTLLoader.js（加载材质）,OBJLoader.js（加载几何体）
 
// eg:只加载几何体模型
  var camera = initCamera(new THREE.Vector3(50, 50, 50));
  var loaderScene = new BaseLoaderScene(camera);
  camera.lookAt(new THREE.Vector3(0, 15, 0));
  // 使用OBJLoader从一个URL加载模型
  var loader = new THREE.OBJLoader();
  // 加载成功触发
  loader.load('../../assets/models/pinecone/pinecone.obj', function (mesh) {

    var material = new THREE.MeshLambertMaterial({
      color: 0x5C3A21
    });

    // loadedMesh is a group of meshes. For 
    // each mesh set the material, and compute the information 
    // three.js needs for rendering.
    mesh.children.forEach(function (child) {
      child.material = material;
      // computeFaceNormals()和computeVertexNormals，这是确保正确渲染使用的材质（THREE.MeshLambertMaterial）所必需的。
      child.geometry.computeVertexNormals();
      child.geometry.computeFaceNormals();
    });

    mesh.scale.set(120,120,120)

    // call the default render loop.
    loaderScene.render(mesh, camera);
  });

// 加载几何体并加载材质
// setup the scene for rendering
  var camera = initCamera(new THREE.Vector3(50, 50, 50));
  var loaderScene = new BaseLoaderScene(camera);
  camera.lookAt(new THREE.Vector3(0, 15, 0));

  var mtlLoader = new THREE.MTLLoader();
  mtlLoader.setPath("../../assets/models/butterfly/")
  mtlLoader.load('butterfly.mtl', function (materials) {
    materials.preload();

    var objLoader = new THREE.OBJLoader();
    objLoader.setMaterials(materials);
    objLoader.load('../../assets/models/butterfly/butterfly.obj', function (object) {

      // move wings to more horizontal position
      [0, 2, 4, 6].forEach(function (i) {
        object.children[i].rotation.z = 0.3 * Math.PI
      });

      [1, 3, 5, 7].forEach(function (i) {
        object.children[i].rotation.z = -0.3 * Math.PI
      });

      // configure the wings,
      var wing2 = object.children[5];
      var wing1 = object.children[4];

      wing1.material.opacity = 0.9;
      wing1.material.transparent = true;
      wing1.material.depthTest = false;
      wing1.material.side = THREE.DoubleSide;

      wing2.material.opacity = 0.9;
      wing2.material.depthTest = false;
      wing2.material.transparent = true;
      wing2.material.side = THREE.DoubleSide;

      object.scale.set(140, 140, 140);
      mesh = object;

      object.rotation.x = 0.2;
      object.rotation.y = -1.3;

      loaderScene.render(mesh, camera);
    });
  });

//【注意】:当收到OBJ、MTL文件以及所需的纹理文件时，必须看一下MTL文件是如何引用纹理的。在MTL文件中应该用相对路径引用纹理文件，而不是绝对路径


2、加载 Collada 模型
	Collada模型（文件扩展名为.dae）是另外一种非常通用的、用来定义场景和模型（以及动画，我们将在下一章讲述）的文件格式。Collada模型中不仅定义了几何体，也定义了材质，甚至还可以定义光源。
	-加载模型时要加载加载器 ColladaLoader.js

// setup the scene for rendering
  var camera = initCamera(new THREE.Vector3(35, 35, 35));
  var loaderScene = new BaseLoaderScene(camera);
  camera.lookAt(new THREE.Vector3(0, 45, 0));

  // load the model
  var loader = new THREE.ColladaLoader();
  loader.load("../../assets/models/medieval/Medieval_building.DAE", function (result) {
   // result对象具有如下结构
   //{
   //    scene: scene,
   //    animations: [...],
   //    kinematic: {...},
   //    library: {...}
   //}

    var sceneGroup = result.scene;
    sceneGroup.children.forEach(function (child) {
      if (child instanceof THREE.Mesh) {
        child.receiveShadow = true;
        child.castShadow = true;
      } else {
        // remove any lighting sources from the model
        sceneGroup.remove(child);
      }
    });

    // correctly scale and position the model
    sceneGroup.rotation.z = 0.5 * Math.PI;
    sceneGroup.scale.set(8, 8, 8);

    // call the default render loop.
    loaderScene.render(sceneGroup, camera);
  });

3、从其他格式文件中加载模型
下面几种文件格式我们只会快速浏览一下，因为它们都遵从相同的原则：
（1）在网页中包含[格式名称]Loader.js文件。
（2）使用[格式名称]Loader.load()函数从URL中加载。、
（3）检查一下传递给回调函数的返回结果，并对它进行渲染

STL、CTM、VTK、AWD、Assimp、VRML、Babylon、3DS、AMF、PlayCanvas、Draco、PRWM、GCode、NRRD、SVG

4、展示蛋白质数据银行中的蛋白质
在Three.js中有一种加载器可以加载PDB格式的文件。PDBLoader.js

5、从PLY模型中创建粒子系统
PLY格式的使用跟其他格式并无很大的区别，只要包含加载器，提供一个回调函数，并渲染模型即可
 // setup the scene for rendering
  var camera = initCamera(new THREE.Vector3(30, 30, 30));
  var loaderScene = new BaseLoaderScene(camera);
  camera.lookAt(new THREE.Vector3(0, 0, 0));

  var loader = new THREE.PLYLoader();

  loader.load("../../assets/models/carcloud/carcloud.ply", function (geometry) {
    var material = new THREE.PointsMaterial({
      color: 0xffffff,
      size: 1,
      opacity: 0.6,
      transparent: true,
      blending: THREE.AdditiveBlending,
      depthWrite: false,
      map: generateSprite()
    });

    var group = new THREE.Points(geometry, material);
    group.scale.set(2.5, 2.5, 2.5);

    loaderScene.render(group, camera);
  });

THREE.PLYLoader来加载模型。回调函数接受geometry作为参数，然后我们用这个geometry作为THREE.Points对象的输入



//【总结】：如果模型不能正确显示，一般是材质设置导致的。可能是用了不兼容的纹理格式，透明度不正确，或者是该格式文件中指向纹理的连接有误。通常可以通过测试材质来检测这种错误，也可以在JavaScript控制台中输出材质信息，看看有没有比较意外的值。要想导出网格和场景，只需要调用scene对象的asJson函数即可；反过来，使用JSONLoader可以将导出的内容重新加载到场景中
```

### 创建动画和移动摄像机

```js
基础动画。
移动摄像机。
变形和骨骼动画。
使用外部模型创建动画




```

#### 基础动画

```js
1、通过requestAnimationFrame，改变属性

var projector = new THREE.Projector();

function onDocumentMouseDown(event) {
	// 基于屏幕上的点击位置会创建一个THREE.Vecor3向量
    var vector = new THREE.Vector3((event.clientX / window.innerWidth) * 2 - 1, -(event.clientY / window.innerHeight) * 2 + 1, 0.5);
    // 将屏幕坐标转换成三维场景中的坐标
    vector = vector.unproject(camera);
	// THREE.Raycaster可以向场景中发射光线
    var raycaster = new THREE.Raycaster(camera.position, vector.sub(camera.position).normalize());
    // raycaster.intersectObjects方法来判断指定的对象中哪些被该光线照射到了
    var intersects = raycaster.intersectObjects([sphere, cylinder, cube]);
	// 将照射到的第一个对象设置透明
    if (intersects.length > 0) {
        console.log(intersects[0]);
        intersects[0].object.material.transparent = true;
        intersects[0].object.material.opacity = 0.1;
    }
}


2、使用Tween.js实现动画
    Tween.js是一个轻量级的JavaScript库。可以很容易地实现某个属性在两个值之间进行过渡，而且起始值和结束值之间的所有中间值都会自动计算出来，这个过程叫作tweening（补间）

var posSrc = { pos: 1}
// 创建了两个补间：tween 和 tweenBack
var tween = new TWEEN.Tween(posSrc).to({pos: 0}, 2000); 
tween.easing(TWEEN.Easing.Bounce.InOut); 

var tweenBack = new TWEEN.Tween(posSrc).to({pos: 1}, 2000); 
tweenBack.easing(TWEEN.Easing.Bounce.InOut); 
// 通过chain()方法可以将这两个补间衔接起来
tweenBack.chain(tween); 
tween.chain(tweenBack); 

tween.start();

// TWEEN.update(); // TWEEN.Update函数令Tween重新计算新的数据变化因数

var onUpdate = function () {
    var count = 0
    var pos = this.pos
    loadedGeometry.vertices.forEach(function(e) {
        var newY = ((e.y + 3.22544)* pos) - 3.22544
        particleCloud.geometry.vertices[count++].set(e.x, newY, e.z)
    })
    particleCloud.sortParticles = true
}
```

#### 使用摄像机

```js
Three.js提供了一些摄像机控件，使用这些控件，你可以控制场景中的摄像机
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/camera.png)

##### ragControls控件类

```
	它虽然也叫作控件但却与其他摄像机控件不同。当用鼠标在场景中拖动时，这个控件会移动场景中的物体而不是摄像机。
除了使用控制器，你还可以通过修改position属性来移动摄像机，通过lookAt()方法来改变摄像机的朝向。

```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/dragControls.png)

```js
1、轨迹球控制器

◉使用TrackballControls
<script src="xxx/TrackballControls.js"> </script>
◉创建控制器，并将它绑定到摄像机上
 var trackballControls = new THREE.TrackballControls(camera);
  trackballControls.rotateSpeed = 1.0;
  trackballControls.zoomSpeed = 1.0;
  trackballControls.panSpeed = 1.0;

◉摄像机的位置更新可以在render循环中完成
var clock = new THREE.Clock();
render();
function render() {
    // clock.getDelta()方法可以精确地计算出此次调用距离上次调用的时间间隔
    // trackballControls.update 更新相机位置
    trackballControls.update(clock.getDelta());
    requestAnimationFrame(render);
    renderer.render(scene, camera)
}  

2、飞行控制器
◉使用TrackballControls
<script src="xxx/flyControls.js"> </script>
◉使用
  var flyControls = new THREE.FlyControls(camera);
  flyControls.movementSpeed = 25;
  // 指向场景所渲染到的元素
  flyControls.domElement = document.querySelector("webgl-output");
  flyControls.rollSpeed = Math.PI / 24;
  flyControls.autoForward = true;
  flyControls.dragToLook = false;

  render();
  function render() {
    flyControls.update(clock.getDelta());
    requestAnimationFrame(render);
    renderer.render(scene, camera)
  } 

3、第一视角控制器
◉使用TrackballControls
<script src="xxx/firstPersonControls.js"> </script>
◉使用
  var fpControls = new THREE.FirstPersonControls(camera);
  fpControls.lookSpeed = 0.4;
  fpControls.movementSpeed = 20;
  fpControls.lookVertical = true;
  fpControls.constrainVertical = true;
  fpControls.verticalMin = 1.0;
  fpControls.verticalMax = 2.0;
  fpControls.lon = -150;
  fpControls.lat = 120;
  
  render();
  function render() {
    fpControls.update(clock.getDelta());
    requestAnimationFrame(render);
    renderer.render(scene, camera)
  }   
4、轨道控制器
◉使用TrackballControls
<script src="xxx/firstPersonControls.js"> </script>
◉使用
  
  var orbitControls = new THREE.OrbitControls(camera);
  orbitControls.autoRotate = true;
  
  render();
  function render() {
    orbitControls.update(clock.getDelta());
    requestAnimationFrame(render);
    renderer.render(scene, camera)
  }   
```

#### 变形动画和骨骼动画

```js
当你使用外部软件（如Blender）创建动画时，通常会有两种主要的动画定义方式
-变形动画
-骨骼动画

1、 用变形目标创建动画


2、用骨骼和蒙皮创建动画
```

#### 使用外部模型创建动画

```js
-带有JSON导出器的Blender
-Collada模型
-MD2模型
-glTF模型
-FBX模型
-DirectX模型
-BVH模型
-SEA模型
-MikuMikuDance模型(但是该模型不允许用于其他商业目的)
```

### 加载和使用纹理

```js
1、加载纹理并应用到网格
	纹理最基础的用法是作为贴图被添加在材质上，当你使用这样的材质创建网格时，网格的颜色则来源于纹理
// 使用THREE.TextureLoader从指定的位置加载图片文件
var textureLoader = new THREE.TextureLoader();
// onLoadFunction在纹理加载完成时被调用；onProgressFunction可以随时汇报加载进度；onErrorFunction在纹理加载或解析出故障时被调用. 三个回调函数可以不传
textureLoader.load("xxx/xxx.jpg", onLoadFunction, onProgressFunction, onErrorFunction)


【注意】但是为了达到最好的效果，最好使用长宽大小为2的次方的正方形图片，例如大小为256×256、512×512、1024×1024的图片最合适

2、使用凹凸贴图创建褶皱
	凹凸贴图用于为材质添加厚度

3、使用法向贴图创建更加细致的凹凸和褶皱
	法线贴图保存的不是高度信息，而是法向量的方向。简单来讲，使用法向贴图只需要使用很少的顶点和面就可以创建出细节很丰富的模型
    
4、使用移位贴图来改变顶点位置
	在Three.js里有一种纹理贴图可以用于修改模型的顶点。法向贴图和凹凸贴图都只能在物体表面生成一种凹凸不平的假象，而移位贴图则能够根据贴图的内容，真正改变模型的形状

5、 用环境光遮挡贴图实现细节阴影
	环境光遮挡技术用于决定模型的哪一部分暴露于环境光之中。

6、 用光照贴图产生假阴影
	光照贴图里面的信息用于指出一个模型的特定部分应该从场景中接收多少光照
    
7、 金属光泽度贴图和粗糙度贴图

8、 Alpha贴图

9、自发光贴图

10 、高光贴图

11 、使用环境贴图创建伪镜面反射效果
	
```

##### 纹理的高级用途

```js
1、自定义UV映射
	
2、 重复纹理

3 、在画布上绘制图案并作为纹理

4 、将视频输出作为纹理
```

### 自定义着色器和后期处理

```
	-不是所有通道的处理结果都会显示在屏幕上。如果想要将通道处理结果输出到屏幕上，你可以使用THREE.ShaderPass和THREE.CopyShader。[插图]效果组合器中通道的顺序是很重要的，因为后一个通道是在前一个通道处理结果的基础上进行处理的。
	-如果想要重用THREE.EffectComposer特定实例的处理结果，你可以使用THREE.TexturePass。
	-如果在THREE.EffectComposer中有多个THREE.RenderPass，需要确保clear属性被设置为false。否则，你只会看到最后一个THREE.RenderPass的处理结果。
	-如果你只想在特定某个物体上应用某种效果，可以使用THREE.MaskPass来实现。当掩码使用完毕，执行THREE.ClearMaskPass来清楚该掩码。
	-Three.js除了提供很多标准通道之外，还有大量的标准着色器，你可以通过THREE.ShaderPass来使用它们。
	-使用Three.js库中的标准方法来创建自定义着色器是很简单的，只需要创建一个片段着色器即可。
	
Physijs库。这个库可以扩展Three.js来实现比如碰撞、重力和约束等物理效果。
```



### 在场景中添加物理效果和声音

```
1、Physijs库，通过该库我们可以添加重力、碰撞和约束等

2、使用THREE.Audio和THREE.AudioListener在场景中添加固定的声源。
```

