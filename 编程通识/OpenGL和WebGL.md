OpenGL, openGl ES, webGl是啥

OpenGL 是一个图形开发库，用于视频，图片处理，2D/3D游戏引擎开发，科学，可视化软件，CAD，AR，VR等等

OpenGl ES 是 OpenGL 的一个子集，主要用于移动平台上的图形绘制编程接口标准

WebGl 是一项用来在网页上绘制的三维，并允许用于与之交互的技术

WebGl 是内嵌在浏览器中，不必安装插件和库就可以直接使用它，我们只需要向已经熟悉的HTML和JS中添加一些额外的三维图形学代码，就可以在网页上显示三维图形


OpenGl，OpenGL ES、WebGL的关系


OpenGL ES可以说是OpenGL为了满足嵌入式设备需求而开发一个特殊版本，是其一个子集；而WebGL，是为了网页渲染效果，将JavaScript和OpenGL ES 2.0结合在一起，通过增加OpenGL ES 2.0的一个JavaScript绑定得到。在PC端web应用中，前端的webgl是通过js语句调用的是OpenGL部分接口，在移动设备是调用OpenGL ES部分接口来实现页面的图形渲染。WebGL只是绑定外面接口的一层，内部的一些核心内容，如着色器，材质，灯光等都是需要借助 GLSL ES 语法来操作的。