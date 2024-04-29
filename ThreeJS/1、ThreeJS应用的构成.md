# `ThreeJS`的应用结构

## 基础

这是`Three.js`系列文章的第一篇。 [`Three.js`](https://threejs.org)是一个尽可能简化在网页端获取`3D` 内容的库。

`Three.js`经常会和`WebGL`混淆， 但也并不总是，`three.js`其实是使用`WebGL`来绘制三维效果的。 [`WebGL`是一个只能画点、线和三角形的非常底层的系统](https://webglfundamentals.org). 想要用`WebGL`来做一些实用的东西通常需要大量的代码， 这就是`Three.js`的用武之地。它封装了诸如场景、灯光、阴影、材质、贴图、空间运算等一系列功能，让你不必要再从底层`WebGL`开始写起。

这套教程假设你已经了解了`JavaScript`，且大部分内容会使用` ES6`的语法。[点击这里查看你需要提前掌握的东西](https://threejs.org/manual/zh/prerequisites.html)。 大部分支持Three.js的浏览器都会自动更新，所以绝大多数用户应该都能运行本套教程的代码。 如果你想在非常老的浏览器上运行此代码， 你需要一个像[`Babel`](http://babeljs.io)一样的语法编译器 。 当然使用非常老的浏览器的用户可能根本不能运行Three.js。

人们在学习大多数编程语言的时候第一件事就是让电脑打印个`"Hello World!"`。 对于三维来说第一件事往往是创建一个三维的立方体。 所以我们从"Hello Cube!"开始。

在我们开始前，让我们试着让你了解一下一个`three.js`应用的整体结构。一个`three.js`应用需要创建很多对象，并且将他们关联在一起。下图是一个基础的`three.js`应用结构。

![img](../images/threejs-structure.svg)

上图需要注意的事项：

- 首先有一个[渲染器(`Renderer`)](https://threejs.org/docs/#api/zh/constants/Renderer)。这可以说是three.js的主要对象。你传入一个[场景(`Scene`)](https://threejs.org/docs/#api/zh/scenes/Scene)和一个[摄像机(`Camera`)](https://threejs.org/docs/#api/zh/cameras/Camera)到[渲染器(`Renderer`)](https://threejs.org/docs/#api/zh/constants/Renderer)中，然后它会将摄像机视椎体中的三维场景渲染成一个二维图片显示在画布上。

- 其次有一个[场景图](https://threejs.org/manual/zh/scenegraph.html) 它是一个树状结构，由很多对象组成，比如图中包含了一个[场景(`Scene`)](https://threejs.org/docs/#api/zh/scenes/Scene)对象 ，多个[网格(`Mesh`)](https://threejs.org/docs/#api/zh/objects/Mesh)对象，[光源(`Light`)](https://threejs.org/docs/#api/zh/lights/Light)对象，[群组(`Group`)](https://threejs.org/docs/#api/zh/objects/Group)，[三维物体(`Object3D`)](https://threejs.org/docs/#api/zh/core/Object3D)，和[摄像机(`Camera`)](https://threejs.org/docs/#api/zh/cameras/Camera)对象。一个[场景(`Scene`)](https://threejs.org/docs/#api/zh/scenes/Scene)对象定义了场景图最基本的要素，并包了含背景色和雾等属性。这些对象通过一个层级关系明确的树状结构来展示出各自的位置和方向。子对象的位置和方向总是相对于父对象而言的。比如说汽车的轮子是汽车的子对象，这样移动和定位汽车时就会自动移动轮子。你可以在[场景图](https://threejs.org/manual/zh/scenegraph.html)的这篇文章中了解更多内容。

- 注意图中[摄像机(`Camera`)](https://threejs.org/docs/#api/zh/cameras/Camera)是一半在场景图中，一半在场景图外的。这表示在three.js中，[摄像机(`Camera`)](https://threejs.org/docs/#api/zh/cameras/Camera)和其他对象不同的是，它不一定要在场景图中才能起作用。相同的是，[摄像机(`Camera`)](https://threejs.org/docs/#api/zh/cameras/Camera)作为其他对象的子对象，同样会继承它父对象的位置和朝向。在[场景图](https://threejs.org/manual/zh/scenegraph.html)这篇文章的结尾部分有放置多个[摄像机(`Camera`)](https://threejs.org/docs/#api/zh/cameras/Camera)在一个场景中的例子。
- [网格(`Mesh`)](https://threejs.org/docs/#api/zh/objects/Mesh)对象可以理解为用一种特定的[材质(`Material`)](https://threejs.org/docs/#api/zh/materials/Material)来绘制的一个特定的[几何体(`Geometry`)](https://threejs.org/manual/zh/Geometry)。[材质(`Material`)](https://threejs.org/docs/#api/zh/materials/Material)和[几何体(`Geometry`)](https://threejs.org/manual/zh/Geometry)可以被多个[网格(`Mesh`)](https://threejs.org/docs/#api/zh/objects/Mesh)对象使用。比如在不同的位置画两个蓝色立方体，我们会需要两个[网格(`Mesh`)](https://threejs.org/docs/#api/zh/objects/Mesh)对象来代表每一个立方体的位置和方向。但只需一个[几何体(`Geometry`)](https://threejs.org/manual/zh/Geometry)来存放立方体的顶点数据，和一种[材质(`Material`)](https://threejs.org/docs/#api/zh/materials/Material)来定义立方体的颜色为蓝色就可以了。两个[网格(`Mesh`)](https://threejs.org/docs/#api/zh/objects/Mesh)对象都引用了相同的[几何体(`Geometry`)](https://threejs.org/manual/zh/Geometry)和[材质(`Material`)](https://threejs.org/docs/#api/zh/materials/Material)。
- [几何体(`Geometry`)](https://threejs.org/manual/zh/Geometry)对象顾名思义代表一些几何体，如球体、立方体、平面、狗、猫、人、树、建筑等物体的顶点信息。Three.js内置了许多[基本几何体](https://threejs.org/manual/zh/primitives.html) 。你也可以[创建自定义几何体](https://threejs.org/manual/zh/custom-buffergeometry.html)或[从文件中加载几何体](https://threejs.org/manual/zh/load-obj.html)。

- [材质(`Material`)](https://threejs.org/docs/#api/zh/materials/Material)对象代表[绘制几何体的表面属性](https://threejs.org/manual/zh/materials.html)，包括使用的颜色，和光亮程度。一个[材质(`Material`)](https://threejs.org/docs/#api/zh/materials/Material)可以引用一个或多个[纹理(`Texture`)](https://threejs.org/docs/#api/zh/textures/Texture)，这些纹理可以用来，打个比方，将图像包裹到几何体的表面。

- [纹理(`Texture`)](https://threejs.org/docs/#api/zh/textures/Texture)对象通常表示一幅要么[从文件中加载](https://threejs.org/manual/zh/textures.html)，要么[在画布上生成](https://threejs.org/manual/zh/canvas-textures.html)，要么[由另一个场景渲染出](https://threejs.org/manual/zh/rendertargets.html)的图像。

- [光源(`Light`)](https://threejs.org/docs/#api/zh/lights/Light)对象代表[不同种类的光](https://threejs.org/manual/zh/lights.html)。

## `Hello,Cube`

![img](images/threejs-1cube-no-light-scene.svg)

#### 加载`Three.js`

```html
<script type="module">
	import * as THREE from 'three';
</script>
```

#### 设置一个`<canvas></canvas>`标签

```html
<canvas id="canvas"></canvas>
```

#### 获取`<canvas></canvas>`元素并传递给`Three.js`

```javascript
import * as THREE from 'three'
const canvas = document.querySelector("#canvas")
const renderer = new THREE.WebGLRenderer({
        antialias:true,
        canvas:canvas
    })
```

拿到canvas后我们需要创建一个[`WebGL`渲染器(`WebGLRenderer`)](https://threejs.org/docs/#api/zh/renderers/WebGLRenderer)。渲染器负责将你提供的所有数据渲染绘制到canvas上。

注意这里有一些细节。如果你没有给`three.js`传`canvas`，`three.js`会自己创建一个  ，但是你必须手动把它添加到文档中。在哪里添加可能会不一样这取决你怎么使用，  我发现给`three.js`传一个`canvas`会更灵活一些。我可以将canvas放到任何地方，  代码都会找到它，假如我有一段代码是将`canvas`插入到文档中，那么当需求变化时， 我很可能必须去修改这段代码。

#### 创建一个[透视摄像机(`PerspectiveCamera`)](https://threejs.org/docs/#api/zh/cameras/PerspectiveCamera)。

```javascript
const fov = 75
const aspect = 2
const near = 0.1
const far = 1000
const camera = new THREE.PerspectiveCamera(fov,aspect,near,far)
```

`fov`是视野范围(`field of view`)的缩写。上述代码中是指垂直方向为75度。 注意three.js中大多数的角用弧度表示，但是因为某些原因透视摄像机使用角度表示。

`aspect`指画布的宽高比。我们将在别的文章详细讨论，在默认情况下 画布是`300x150`像素，所以宽高比为`300/150`或者说`2`。

`near`和`far`代表近平面和远平面，它们限制了摄像机面朝方向的可绘区域。 任何距离小于或超过这个范围的物体都将被裁剪掉(不绘制)。

这四个参数定义了一个 *"视椎(`frustum`)"*。 *视椎(`frustum`)*是指一个像被削去顶部的金字塔形状。换句话说，可以把"视椎(frustum)"想象成其他三维形状如球体、立方体、棱柱体、截椎体。 

![img](images/frustum-3d.svg)

近平面和远平面的高度由视野范围决定，宽度由视野范围和宽高比决定。

视椎体内部的物体将被绘制，视椎体外的东西将不会被绘制。

摄像机默认指向Z轴负方向，上方向朝向Y轴正方向。我们将会把立方体放置在坐标原点，所以我们需要往后移一下摄像机才能显示出物体。

```javascript
camera.position.z = 2
```

