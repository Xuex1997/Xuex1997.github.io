---
layout: post
title: WebGL和Three.js学习笔记
date: 2018-07-04
categories: 计算机图形学
tags: WebGL, Three.js
---
# WebGL和Three.js学习笔记
#### 注：跨域问题解决方法
在命令行里输入，在打开的浏览器中打开html

```
open -n /Applications/Google\ Chrome.app/ --args --disable-web-security  --user-data-dir=/Users/xue/Desktop/MyChromeDevUserData/
```
## 1.1  什么是WebGL和Three.js
### 1.1.1 什么是WebGL
`WebGL`是基于`OpenGL ES 2.0`的`Web`标准，可以通过`HTML5 Canvas`元素作为`DOM`接口访问。`WebGL`可以看做是将`OpenGL ES`（OpenGL for Embedded Systems，OpenGL嵌入式版本，针对手机、游戏机等设备相对较轻量级的版本）移植到了网页平台，像Chrome、Firefox这些现代浏览器都实现了`WebGL`标准，使用`JavaScript`就可以用你熟悉的、类似`OpenGL`的代码编写了。
### 1.1.2 什么是Three.js
    Three.js是一个3D JavaScript库。
`Three.js`封装了底层的图形接口，使得程序员能够在无需掌握繁冗的图形学知识的情况下，也能用简单的代码实现三维场景的渲染。我们都知道，更高的封装程度往往意味着灵活性的牺牲，但是`Three.js`在这方面做得很好。几乎不会有`WebGL`支持而`Three.js`实现不了的情况，而且就算真的遇到这种情况，你还是能同时使用`WebGL`去实现，而不会有冲突。当然，除了`WebGL`之外，`Three.js`还提供了基于`Canvas`、`SVG`标签的渲染器，但由于通常`WebGL`能够实现更灵活的渲染效果，所以本书主要针对基于`WebGL`渲染器进行说明。
## 1.2 使用Three.js
### 1.2.1 准备工作
* 开发环境
  `Three.js`是一个`JavaScript`库。使用ATOM。

* 下载    
  首先，我们需要在Github下载Three.js的代码。在[Three.js](thttps://github.com/mrdoob/three.js/tree/master/build)可以看到three.js和three.min.js两个文件，前者是没有经过代码压缩的，因此适用于调试阶段；后者是经过代码压缩的，调试起来会不太方便，但文件较小，适用于最终的发布版。保存一个文件到本地，这里我们可以选择three.js。

* 引用    
  在使用Three.js之前，我们需要在HTML文件中引用该文件：
`<script type="text/javascript" src="../js/three.js"></script>`    
然后就能通过全局变量THREE访问到所有属性和方法了。 
### 1.2.2 Hello, world!
* 首先，在`HTML`的`<head>`部分，需要声明外部文件`three.js`。

  ```
  <head>
    <script type="text/javascript" src="../js/three.js"></script>
  </head>
  ```
* `WebGL`的渲染是需要`HTML5 Canvas`元素的，你可以手动在`HTML`的`<body>`部分中定义`Canvas`元素，或者让`Three.js`帮你生成。
  * 手动在HTML中定义：

    ```
    <body onload="init()">
      <canvas id="mainCanvas" width="400px" height="300px" ></canvas>
    </body>
    ```
    在JavaScript代码中定义一个init函数，在HTML加载完后执行：
    ```
    function init() {
      // ...
    }
    ```
 
* 一个典型的`Three.js`程序至少要包括 **渲染器（Renderer）**、**场景（Scene）**、**照相机（Camera）**，以及在场景中创建的物体。
	* **渲染器（Renderer）** 
	渲染器决定了渲染的结果应该画在页面的什么元素上面，并且以怎样的方式来绘制。       
	渲染器将和`Canvas`元素进行绑定，如果之前在HTML中手动定义了id为`mainCanvas`的`Canvas`元素，那么`Renderer`可以这样写：
	
    	```
    	var renderer = new THREE.WebGLRenderer({
    		canvas: document.getElementById('mainCanvas')
    	});
    	```
    而如果想要Three.js生成Canvas元素，在HTML中就不需要定义Canvas元素，在JavaScript代码中可以这样写：

    	```
    	var renderer = new THREE.WebGLRenderer();
    	renderer.setSize( window.innerWidth, window.innerHeight );
    	document.body.appendChild(renderer.domElement);
    	```
    上面代码的第二行表示设置`Canvas`的宽400像素，高300像素。第三行将渲染器对应的`Canvas`元素添加到`<body>`中。 渲染器`renderer`的`domElement`元素，表示渲染器中的画布，所有的渲染都是画在`domElement`上的，所以这里的`appendChild`表示将这个`domElement`挂接在`body`下面，这样渲染的结果就能够在页面中显示了。  
    我们可以使用下面的代码将背景色（用于清除画面的颜色）设置为黑色：
    ```
    renderer.setClearColor(0x000000);
    ```
     
  * **场景（Scene）**    
    在`Three.js`中添加的物体都是添加到场景中的，因此它相当于一个大容器。一般说，场景里没有很复杂的操作，在程序最开始的时候进行实例化，然后将物体添加到场景中即可。场景是所有物体的容器，如果要显示一个苹果，需要讲苹果对象加入场景中。
    
    	```
    	var scene = new THREE.Scene();
    	```
	* **照相机（Camera）**  
		相机决定了场景中哪个角度的景色会显示出来。在`Threejs`中相机的表示是`THREE.Camera`，它是相机的抽象基类，其子类有两种相机，分别是正投影相机`THREE.OrthographicCamera`和透视投影相机`THREE.PerspectiveCamera`。
		WebGL和Three.js使用的坐标系是右手坐标系，看起来就是这样的：    
		![](http://www.ituring.com.cn/download/01YmPz57VN7b)
     
    	```
    	var camera = new THREE.PerspectiveCamera(45, 4 / 3, 1, 1000);
    	camera.position.set(0, 0, 5);
    	scene.add(camera);
    	```
    	! 值得注意的是，照相机也需要被添加到场景中。  
    
		* 正投影的构造函数如下所示：
	
			```
			OrthographicCamera( left, right, top, bottom, near, far )
			```
		![](http://www.hewebgl.com/attached/image/20130530/20130530145859_920.jpg)
		* 透视投影相机的构造函数如下所示：
		
			```
			PerspectiveCamera( fov, aspect, near, far )
			```
			
			* 视角fov：眼睛睁开的角度,即,视角的大小,如果设置为0,相当你闭上眼睛了,所以什么也看不到,如果为180,那么可以认为你的视界很广阔,但是在180度的时候，往往物体很小，因为他在你的整个可视区域中的比例变小了。
			* 近平面near：表示近处的裁面的距离。
			* 远平面far：表示远处的裁面,
			* 纵横比aspect：实际窗口的纵横比，即宽度除以高度。

* **添加物体到场景中**     
  
  ```
  var geometry = new THREE.CubeGeometry(1,1,1); 
  var material = new THREE.MeshBasicMaterial({color: 0x00ff00});
  var cube = new THREE.Mesh(geometry, material); 
  scene.add(cube);
  ```
  `CubeGeometry`是一个正方体或者长方体，由它的3个参数所决定，cubeGeometry的原型如下代码所示：
  
	```
	CubeGeometry(width, height, depth, segmentsWidth, segmentsHeight, segmentsDepth, materials, sides)
	```

	* width：立方体x轴的长度
	* height：立方体y轴的长度
	* depth：立方体z轴的深度，也就是长度

  `MeshBasicMaterial`是一种材质，可以用来设置物体的颜色。  
  这里的长度是 **在物体坐标系** 中的，**其单位与屏幕分辨率等无关**，简单地说，它就是一个虚拟空间的坐标系，1代表多少并没有实际的意义，而重要的是相对长度。

* **渲染**    
  在定义了场景中的物体，设置好的照相机之后，渲染器就知道如何渲染出二维的结果了。这时候，我们只需要调用渲染器的渲染函数，就能使其渲染一次了。
  
  ```
  renderer.render(scene, camera);
  ```
	渲染函数的原型如下：
  `render( scene, camera, renderTarget, forceClear )`
	各个参数的意义是：
	* scene：前面定义的场景
	* camera：前面定义的相机
	* renderTarget：渲染的目标，默认是渲染到前面定义的render变量中
	* forceClear：每次绘制之前都将画布的内容给清除，即使自动清除标志autoClear为false，也会清除。
* **渲染循环**  
	渲染有两种方式：实时渲染和离线渲染 。   
	离线渲染是事先渲染好一帧一帧的图片，然后再把图片拼接成动画的。实时渲染就是需要不停的对画面进行渲染，即使画面中什么也没有改变，也需要重新渲染。下面就是一个渲染循环：

	```
	function render() {
		cube.rotation.x += 0.1;
		cube.rotation.y += 0.1;
		renderer.render(scene, camera);
		requestAnimationFrame(render);
	}
	```
	其中一个重要的函数是`requestAnimationFrame`，这个函数就是让浏览器去执行一次参数中的函数，这样通过上面`render`中调用`requestAnimationFrame()`函数，`requestAnimationFrame()`函数又让`rander()`再执行一次，就形成了我们通常所说的渲染循环了。

* **场景，相机，渲染器之间的关系**   
	Three.js中的场景是一个物体的容器，开发者可以将需要的角色放入场景中，例如苹果，葡萄。同时，角色自身也管理着其在场景中的位置。相机的作用就是面对场景，在场景中取一个合适的景，把它拍下来。渲染器的作用就是将相机拍摄下来的图片，放到浏览器中去显示。他们三者的关系如下图所示：
	![](http://www.hewebgl.com/attached/image/20130810/20130810150021_257.jpg)


## 1.3 Three.js功能概览
Three.js官网文档中的一些重要的对象

* Cameras（照相机，控制投影方式）
  * Camera
  * OrthographicCamera
  * PerspectiveCamera

* Core（核心对象）
  * BufferGeometry
  * Clock（用来记录时间）
  * EventDispatcher
  * Face3
  * Face4
  * Geometry
  * Object3D
  * Projector
  * Raycaster（计算鼠标拾取物体时很有用的对象）

* Lights（光照）
  * Light
  * AmbientLight
  * AreaLight
  * DirectionalLight
  * HemisphereLight
  * PointLight
  * SpotLight

* Loaders（加载器，用来加载特定文件）
  * Loader
  * BinaryLoader
  * GeometryLoader
  * ImageLoader
  * JSONLoader
  * LoadingMonitor
  * SceneLoader
  * TextureLoader

* Materials（材质，控制物体的颜色、纹理等）
  * Material
  * LineBasicMaterial
  * LineDashedMaterial
  * MeshBasicMaterial
  * MeshDepthMaterial
  * MeshFaceMaterial
  * MeshLambertMaterial
  * MeshNormalMaterial
  * MeshPhongMaterial
  * ParticleBasicMaterial
  * ParticleCanvasMaterial
  * ParticleDOMMaterial
  * ShaderMaterial
  * SpriteMaterial

* Math（和数学相关的对象）

  * Box2
  * Box3
  * Color
  * Frustum
  * Math
  * Matrix3
  * Matrix4
  * Plane
  * Quaternion
  * Ray
  * Sphere
  * Spline
  * Triangle
  * Vector2
  * Vector3
  * Vector4

* Objects（物体）

  * Bone
  * Line
  * LOD
  * Mesh（网格，最常用的物体）
  * MorphAnimMesh
  * Particle
  * ParticleSystem
  * Ribbon
  * SkinnedMesh
  * Sprite

* Renderers（渲染器，可以渲染到不同对象上）

  * CanvasRenderer
  * WebGLRenderer（使用WebGL渲染，这是本书中最常用的方式）
  * WebGLRenderTarget
  * WebGLRenderTargetCube
  * WebGLShaders（着色器，在最后一章作介绍）

* Renderers / Renderables

  * RenderableFace3
  * RenderableFace4
  * RenderableLine
  * RenderableObject
  * RenderableParticle
  * RenderableVertex

* Scenes（场景）

  * Fog
  * FogExp2
  * Scene

* Textures（纹理）

  * CompressedTexture
  * DataTexture
  * Texture

* Extras

  * FontUtils
  * GeometryUtils
  * ImageUtils
  * SceneUtils

* Extras / Animation

  * Animation
  * AnimationHandler
  * AnimationMorphTarget
  * KeyFrameAnimation

* Extras / Cameras

  * CombinedCamera
  * CubeCamera

* Extras / Core

  * Curve
  * CurvePath
  * Gyroscope
  * Path
  * Shape

* Extras / Geometries（几何形状）

  * CircleGeometry
  * ConvexGeometry
  * CubeGeometry
  * CylinderGeometry
  * ExtrudeGeometry
  * IcosahedronGeometry
  * LatheGeometry
  * OctahedronGeometry
  * ParametricGeometry
  * PlaneGeometry
  * PolyhedronGeometry
  * ShapeGeometry
  * SphereGeometry
  * TetrahedronGeometry
  * TextGeometry
  * TorusGeometry
  * TorusKnotGeometry
  * TubeGeometry

* Extras / Helpers

  * ArrowHelper
  * AxisHelper
  * CameraHelper
  * DirectionalLightHelper
  * HemisphereLightHelper
  * PointLightHelper
  * SpotLightHelper

* Extras / Objects

  * ImmediateRenderObject
  * LensFlare
  * MorphBlendMesh

* Extras / Renderers / Plugins

  * DepthPassPlugin
  * LensFlarePlugin
  * ShadowMapPlugin
  * SpritePlugin

* Extras / Shaders

  * ShaderFlares
  * ShaderSprite



## 画一条直线
* 声明一个几何体geometry，如下：   
	`var geometry = new THREE.Geometry();`    
	几何体里面有一个vertices变量，可以用来存放点。
* 定义线条的材质，使用`THREE.LineBasicMaterial`类型来定义，它接受一个集合作为参数，其原型如下：    
	`LineBasicMaterial( parameters )`     
	Parameters是一个定义材质外观的对象，它包含多个属性来定义材质，这些属性是：
	* Color：线条的颜色，用16进制来表示，默认的颜色是白色。
	* Linewidth：线条的宽度，默认时候1个单位宽度。
	* Linecap：线条两端的外观，默认是圆角端点，当线条较粗的时候才看得出效果，如果线条很细，那么你几乎看不出效果了。
	* Linejoin：两个线条的连接点处的外观，默认是“round”，表示圆角。
	* VertexColors：定义线条材质是否使用顶点颜色。
	* Fog：定义材质的颜色是否受全局雾效的影响。  
	 
	这里使用了顶点颜色`vertexColors: THREE.VertexColors`，就是线条的颜色会根据顶点来计算。
	
	```
	var material = new
	THREE.LineBasicMaterial( { vertexColors: THREE.VertexColors } );
	```

* 接下来，定义两种颜色，分别表示线条两个端点的颜色，如下所示：
	
	```
	var color1 = new THREE.Color( 0x444444 ),
	color2 = new THREE.Color( 0xFF0000 );
	```
* 定义2个顶点的位置，并放到`geometry`中，代码如下：
		
	```
	var p1 = new THREE.Vector3( -100, 0, 100 );
	var p2 = new THREE.Vector3( 100, 0, -100 );

	geometry.vertices.push(p1);
	geometry.vertices.push(p2);
	```
* 为4中定义的2个顶点，设置不同的颜色，代码如下所示：

	```
	geometry.colors.push( color1, color2 );    
	```
	`geometry`中`colors`表示顶点的颜色，必须材质中`vertexColors`等于`THREE.VertexColors` 时，颜色才有效，如果`vertexColors`等于`THREE.NoColors`时，颜色就没有效果了。那么就会去取材质中`color`的值。

* 定义一条线，使用`THREE.Line`类，代码如下所示：

	```
	var line = new THREE.Line( geometry, material, THREE.LinePieces );
	```
	第一个参数是几何体`geometry`，里面包含了2个顶点和顶点的颜色。第二个参数是线条的材质，或者是线条的属性，表示线条以哪种方式取色。第三个参数是一组点的连接方式。然后，将这条线加入到场景中，代码如下：
		
	```
	scene.add(line);
	```
	这样，场景中就会出现刚才的那条线段了。
* 代码：

	```
function initObject() {
	var geometry = new THREE.Geometry();
	var material = new THREE.LineBasicMaterial( { vertexColors: THREE.VertexColors} );
	var color1 = new THREE.Color( 0x444444 ), color2 = new THREE.Color( 0xFF0000 );
	// 线的材质可以由2点的颜色决定
	
	var p1 = new THREE.Vector3( -100, 0, 100 );
	var p2 = new THREE.Vector3(  100, 0, -100 );
	
	geometry.vertices.push(p1);
	geometry.vertices.push(p2);
	geometry.colors.push( color1, color2 );

	var line = new THREE.Line( geometry, material, THREE.LineSegments );
	scene.add(line);
}
	```

## Three.js创建的3D场景中常用到的插件
* dat.gui.js用于创建菜单栏，可以用来控制场景中的各个参数来调试场景。
	* 需要引擎js文件，如下：
	
		```
		<script src="../lib/dat.gui.js"></script>
		```
	* 定义要控制的参数，以相机的位置为例

		```
		var controls = new function() {  
			this.cx = 0;  
			this.cy = 0;  
			this.cz = 0;  
			this.redraw = function() {  
				camera.position.set(controls.cx, controls.cy, controls.cz);  
		} 
		``` 
		cx、cy、cz为相机的x、y、z轴坐标。初始值为前面场景初始化定义相机的位置。
`redraw`是当参数变化时的重绘函数。
	* 创建面板

		```
		var gui = new dat.GUI();  
		gui.add(controls, 'cx', -100, 100).onChange(controls.redraw);  
		gui.add(controls, 'cy', -100, 100).onChange(controls.redraw);  
		gui.add(controls, 'cz', -100, 100).onChange(controls.redraw);
		``` 
		往菜单中添加cx、cy、cz三个参数，定义参数变化范围是`-100~100`, 定义参数变化时调用redraw()函数。
* `stats.js`用于对JavaScript进行性能检测。
	* 需要引擎js文件，如下：
	
		```
		<script src="../lib/stas.min.js"></script>
		```
	* 实例化一个stats对象

		```
		var stats = new Stats();  
		stats.setMode(0);// 0: fps, 1: ms
		// 将stats的界面对应左上角   
		stats.domElement.style.position = 'absolute'; 
		stats.domElement.style.left = '0px';  
		stats.domElement.style.top = '0px';  
		document.body.appendChild(stats.domElement);  
		```
	* 更新

		```
		function render() {  
			requestAnimationFrame(render);  
			renderer.render(scene, camera);  
			stats.update();
		}
		```  
		调用update()函数实时更新。

* **使用动画引擎Tween.js来创建动画**   
	可以通过移动相机和移动物体来产生动画的效果。使用的方法是在渲染循环里去移动相机或者物体的位置。如果动画稍微复杂一些，这种方式实现起来就比较麻烦一些了。为了使程序编写更容易一些，可以使用动画引擎来实现动画效果。和three.js紧密结合的动画引擎是Tween.js。对于快速构件动画来说，Tween.js是一个容易上手的工具。
	* 首先，你需要引擎js文件，如下：
	
		```
		<script src="../lib/Tween.js"></script>
		```
	* 第二步，就是构件一个Tween对象，对Tween进行初始化
	
		```
		function initTween() {
			new TWEEN.Tween(mesh.position).to( { x: -400 }, 3000 ).repeat( Infinity ).start();
		}
		```
	`TWEEN.Tween`的构造函数接受的是要改变属性的对象，这里传入的是`mesh`的位置。`Tween`的任何一个函数返回的都是自身，所以可以用串联的方式直接调用各个函数。
	
		* to函数，接受两个参数，第一个参数是一个集合，里面存放的键值对，键x表示mesh.position的x属性，值-400表示，动画结束的时候需要移动到的位置。第二个参数，是完成动画需要的时间，这里是3000ms。
		* repeat( Infinity )表示重复无穷次，也可以接受一个整形数值，例如5次。
		* Start表示开始动画，默认情况下是匀速的将mesh.position.x移动到-400的位置。

	* 第三步是，需要在渲染函数中去不断的更新Tween，这样才能够让`mesh.position.x`移动位置:

		```
		function animation() {
			renderer.render(scene, camera);
			requestAnimationFrame(animation);
			stats.update();
			TWEEN.update();
		}
		```

## Threejs中的各种光源


* 1、 光源基类  
	在Threejs中，光源用Light表示，它是所有光源的基类。它的构造函数是：
	
	```
	THREE.Light ( hex )
	```
	它有一个参数hex，接受一个16进制的颜色值。例如要定义一种红色的光源，我们可以这样来定义：Var redLight = new THREE.Light(0xFF0000);

* 2、 由基类派生出来的其他种类光源    
	THREE.Light只是其他所有光源的基类，要让光源除了具有颜色的特性之外，我们需要其他光源。下图是目前光源的继承结构。    
	![](http://www.hewebgl.com/attached/image/20130515/20130515163339_12.jpg)
	* 环境光   
		环境光是经过多次反射而来的光称为环境光，无法确定其最初的方向。环境光是一种无处不在的光。环境光源放出的光线被认为来自任何方向。    
		环境光用`THREE.AmbientLight`来表示，它接受一个16进制的颜色值，作为光源的颜色。环境光将照射场景中的所有物体，让物体显示出某种颜色。环境光的使用例子如下所示：

		```
		var light = new THREE.AmbientLight( 0xff0000 );
		scene.add( light );
		```
		只需要将光源加入场景，场景就能够通过光源渲染出好的效果来了。

	* 点光源: 由这种光源放出的光线来自同一点，且方向辐射自四面八方。例如蜡烛放出的光，萤火虫放出的光。点光源用`PointLight`来表示，它的构造函数如下所示：
		
		```
		PointLight( color, intensity, distance )
		```
		参数:
		
		* Color：光的颜色
		* Intensity：光的强度，默认是1.0,就是说是100%强度的灯光，
		* distance：光的距离，从光源所在的位置，经过distance这段距离之后，光的强度将从Intensity衰减为0。 默认情况下，这个值为0.0，表示光源强度不衰减。

	* 聚光灯：这种光源的光线从一个锥体中射出，在被照射的物体上产生聚光的效果。使用这种光源需要指定光的射出方向以及锥体的顶角α。聚光灯的构造函数是：
		
		```
		THREE.SpotLight( hex, intensity, distance, angle, exponent )
		```
		函数的参数如下所示：
		
		* Hex：聚光灯发出的颜色，如0xFFFFFF
		* Intensity：光源的强度，默认是1.0，如果为0.5，则强度是一半，意思是颜色会淡一些。和上面点光源一样。
		* Distance：光线的强度，从最大值衰减到0，需要的距离。 默认为0，表示光不衰减，如果非0，则表示从光源的位置到Distance的距离，光都在线性衰减。到离光源距离Distance时，光源强度为0.
		* Angle：聚光灯着色的角度，用**弧度**作为单位，这个角度是和光源的方向形成的角度。
		* exponent：衰减参数，越大衰减越快。
	* 平行光: 是一组没有衰减的平行的光线，类似太阳光的效果。构造函数如下所示：
		
		```
		THREE.DirectionalLight = function ( hex, intensity )
		```
		其参数如下：
		
		* Hex：关系的颜色，用16进制表示
		* Intensity：光线的强度，默认为1。因为RGB的三个值均在0~255之间，不能反映出光照的强度变化，光照越强，物体表面就更明亮。它的取值范围是0到1。如果为0，表示光线基本没什么作用，那么物体就会显示为黑色。
		* 注意⚠️：方向由位置和原点（0,0,0）来决定，平行光只与方向有关，与离物体的远近无关。分别将平行光放到（0,0,100），（0,0,50），（0,0,25），（0,0,1），渲染的结果是一样的，颜色的深浅不与离物体的距离相关。




## Three.js Raycaster 射线 碰撞检测

* 用Raycaster来检测碰撞的原理很简单，我们需要以物体的中心为起点，向各个顶点发出射线，然后检查射线是否与其它的物体相交。如果出现了相交的情况，检查最近的一个交点与射线起点间的距离，如果这个距离比射线起点至物体顶点间的距离要小，则说明发生了碰撞。

* 这个方法有一个缺点 ，当物体的中心在另一个物体内部时，是不能够检测到碰撞的。而且当两个物体能够互相穿过，且有较大部分重合时，检测效果也不理想。

* Raycaster申明    

	```
	Raycaster( origin, direction, near, far ) { }
	```
	
	* origin — 射线的起点向量。
	* direction — 射线的方向向量，应该归一化。
	* near — 所有返回的结果应该比 near 远。Near不能为负，默认值为0。
	* far — 所有返回的结果应该比 far 近。Far 不能小于 near，默认值为无穷大。  

在我们的大作业中，是要检测控制器是否碰撞到物体，由于控制器没有大小，所以用一个`group`来模拟一个摄像机盒，将控制器的位置赋给`group`，然后获得控制器的朝向，并设置水平射线
	
```
//添加辅助线，用来标示摄像机，因为摄像机没有空间大小，所以用这个来模拟一个摄像机盒
group = new THREE.Group();
up = new THREE.ArrowHelper(new THREE.Vector3(0, 1, 0), new THREE.Vector3(), 10, 0x00ff00);
horizontal = new THREE.ArrowHelper(new THREE.Vector3(1, 0, 0), new THREE.Vector3(), 10, 0x00ffff);
down = new THREE.ArrowHelper(new THREE.Vector3(0, -1, 0), new THREE.Vector3(), 10, 0xffff00);

group.add(up);
group.add(horizontal);
group.add(down);

...

rotation.copy(control.getWorldDirection().multiply(new THREE.Vector3(-1, 0, -1)));
...
rotation.applyMatrix4(m);

horizontalRaycaster.set(control.position , rotation );

```

获得射线之后就可以查看在射线于其他物体有没有交点了，没有交点的话就可以移动了：

```
var horizontalIntersections = horizontalRaycaster.intersectObjects(scene.children, true);
// 查看返回结果
var horOnObject = horizontalIntersections.length > 0;

if(!horOnObject){
	if ( moveForward || moveBackward ) velocity.z -= direction.z * speed * delta;
	if ( moveLeft || moveRight ) velocity.x -= direction.x * speed * delta;
}
```
	