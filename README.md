# three.js-Spin-.obj-on-hover
Show the steps to make an imported .obj and .mtl spin when your mouse hover!
=

# The model can only be shown on Server like TOMCAT 
# 必须导入类似TOMCAT的服务器才能正常显示模型！代码经测试可跑，如果出问题可能是`路径设置不正确`


最终可以实现这样的效果————外部导入的obj模型随着鼠标移动轻微转动。
![Image text](https://github.com/Tanzyingsz/three.js-Spin-.obj-on-hover/blob/master/demo_img/SpinOnHover.gif)

因为上图移动不太明显，追加一下另外一个模型的效果
![Image text](https://github.com/Tanzyingsz/three.js-Spin-.obj-on-hover/blob/master/demo_img/SpinOnHover_Lion.gif)

代码都放在demo.html里面了，因为最终仅显示图片，故把辅助功能gui和stats关闭了，但是js包里面仍然放了，去掉注释就可以看到“帧数”。

除了常规的初始化舞台、灯光、相机，本例重点对controls和鼠标移动进行控制。

# STEP 1 加载材质及贴图 
`务必将建模时导出的.png材质文件也放入模型同一个文件夹内，不然会出错`
` object.scale.set(1.5, 1.5, 1.5); 请根据你自己的模型调整！`

```javascript
function initModel() {

        var mtlLoader = new THREE.MTLLoader();
        mtlLoader.setPath('models/');
        mtlLoader.load('fu.mtl', function (material) {
            var objLoader = new THREE.OBJLoader();
            objLoader.setMaterials(material);            //设置当前加载的纹理
            objLoader.setPath('models/');
            objLoader.load('fu.obj', function (object) {
                object.scale.set(1.5, 1.5, 1.5);         //将模型缩放并添加到场景当中，参数改动方可让你的模型正常显示！
                object.rotateY(-Math.PI/8);
                object.position.y = -10;
       
                scene.add(object);
            })
        });
        
    }
```

# STEP 2 增加鼠标监听

```javascript

document.addEventListener('mousemove', onDocumentMouseMove, false); //initRender()初始化时加入，监听鼠标的移动

    function onDocumentMouseMove(event) {
  	  mouseX = (event.clientX - windowHalfX) / 20; //改自https://codepen.io/tanz_ying/pen/qgwEr
  	  mouseY = (event.clientY - windowHalfY) / 20; //数字小会导致鼠标移过的时候模型突然放大，所以把数字加大一些。
  }
```

# STEP 3 限制用户的控制
网上很多代码都是拖拽物体360°移动，而我们要实现的效果仅仅是`鼠标移动的时候，模型跟着动`，因此需要禁用一些参数。
PS.好好看three.js的文档https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene 你就会发现这些里面其实都有啊啊啊啊根本不需要上网另外找教程！！！

```javascript
    var controls;
    function initControls() {

        controls = new THREE.OrbitControls( camera, renderer.domElement );
       
        controls.enableDamping = false;//使动画循环使用时阻尼或自转 意思是否有惯性 
        controls.minDistance  = 100;   //设置相机距离原点的最远距离
        controls.maxDistance  = 500;  //设置相机距离原点的最远距离 
        
       
       /**以下是重点！**/
        controls.enabled = false;     //是否可以鼠标操控!!!就是这个point没注意，导致我找了两个小时没有找到解决方法！
        controls.enableZoom = false;   //是否可以缩放 >>禁用就不能滚轮操作了
        controls.autoRotate = false;   //是否自动旋转 >>禁用模型就不会自己乱转
        controls.enablePan = false;   //是否开启右键拖拽
        controls.maxAzimuthAngle=Math.PI/2; //最大旋转角，范围为-Math.PI到Math.PI，默认无穷大 >> 防止模型屁股见用户

    }
```

## 补充
获得透明背景的方法如下
```javascript
    renderer.setClearColor( 0x000000, 0 ); 
```
想要彩色背景的话，改0x00000为你想要的颜色就好啦。

这里网页小萌新，代码都是在网上开源代码的基础上改的，希望能帮助到同样纠结“网上那些炫酷效果到底怎么实现的啊啊啊啊”的小伙伴！

