# WebGL For All

[Original URL](http://ipestov.com/webgl-for-all/)

> I decided to write this article after trying to get known of WebGL for a long time. It turned out that people which didn't have any deals with 3D graphics before don't know how it actually works....

![WebGL For All](http://ipestov.com/content/images/2016/02/triangle-power.jpg)

I decided to write this article after trying to get known of WebGL for a long time. It turned out that people which didn't have any deals with 3D graphics before don't know how it actually works.

WebGL, not as JavaScript, is not very popular now and there are not too many articles about it. Most of posts skip too much information and go to the special library they want to cover. We all know that big and powerful instruments are not always useful for our specific tasks or do them slow and something like it.

This article will take you through the **basics of WebGL** and tell you the starting point of learning it.

WebGL was developed to draw graphics right in the browser using the video card ant not processor of the computer. If you don't know what does it mean the I suggest you seeing [this](https://www.youtube.com/watch?v=-P28LKWTzrI) short video.

**WebGL is based on OpenGL ES 2.0** which is a special version for mobile devices. WebGL specs were released in 2011, they are developed by non-commercial Kronos Group. It is known that a new specs 2.0 are now being developed.

![List of supported browsers](http://ipestov.com/content/images/2016/02/5da2fd9ae87941619424d82e8a48836d.png) _List of supported browsers from [caniuse.com](http://caniuse.com)._

**WebGL is supported by most of current browsers (about 83%)**. The positive fact is that you will support only newest browsers and will forget about scary ECMAScript 3.

If you think that WebGL is painting in 3D then you're wrong. **WebGL knows nothing about 3D**. It's something like low-level 2D API and it can draw triangles only. But he can do it a lot and very fast.

Do you want to have a square? Now problems, just connect two triangles.

## How to draw a triangle

Because all shapes in WebGL are based on triangles we will describe how to work with them.

Unlike OpenGL, WebGL uses only shaders. Shaders are not connected with shades or shading (as you might thought). Probably they were thought to paint shades but currently they are just painting all around.

Actually **shader is a program which is executed on your video card** and it is written in GLSL. This language is rather easy and learning it is not a problem.

There are two types of shaders: _apical_ and _fragmental_. Let's see them one by one.

To understand the nub of how apical shader works we will forget about our current topic. Imagine that we're painting a cube or any other shape with a set of vertices. To do that we have to set its geometry and it is done in coordinates. It would be hard to always calculate the position of each vertex so we ask the video card to do it instead of processor, so we have apical shader for it.

It receives all coordinates of vertices and position of local coordinate system which is used to define all these dots. This shader is called for each vertex and calculates their positions and passes them to a **fragmental shader** then.

**Apical shader** always calculates vertices positions but it can do more tasks at the same time: for example, it can think about the angle of the light fall. Some enthusiasts do incredible things using apical shaders. See [the examples](https://www.chromeexperiments.com/webgl).

We can't paint a shape by it's position only. We should also know of how to paint it so for this reason **fragmental shader** was developed. It is called for each dot of the shape and tells in what color should it be painted.

![Apical and fragmental shaders](http://ipestov.com/content/images/2016/02/89627b5df212491aa031e6b33a3a1847.png)

_The example of how two types of shaders work_.

How it was said before, shaders are written in **GLSL**. Let's see an example of the code.

Apical shader example:

```
attribute vec3 a_position; 
attribute vec3 a_color; 
uniform vec3 u_position; 
varying vec3 v_color;

void main(void) { 
 v_color = a_color;
 gl_Position = vec4(u_position + a_position, 1.0);
}
```

Fragmental shader example:

```
precision mediump float; 
varying vec3 v_сolor;

void main(void) { 
 gl_FragColor = vec4(v_color.rgb, 1.0);
}
```

The code has variables and the main function, which returns the result of the shader's work: **l_Position** is responsible for coordinates and **gl_FragColor** tells the needed color.

Shaders have three types of variables which are given by the main program:

1. **attributes** are available only for apical shaders and are different for each of the vertex.
2. **uniforms** are available for both shaders and they're same for all calls.
3. **varying** are used to retrieve information from apical to fragmental shader.

When calling fragmental shader for specific dot, the value of **varying** are linearly interpolated between the vertices of the triangle, in which our dot lies.

![Varying is calculated from the vertices variyng value](http://ipestov.com/content/images/2016/02/ddc37d94ec0a407285bc917870c26100.png)

***varying** value is done by the value of the same variable for all three vertices*.

Now we're going to initialize those shaders. First of all, we will get the context of WebGL:

```
var gl = canvas.getContext(‘webgl’); 
```

Shaders' codes are defined as a simple line and should be compiled:

```
var vertexShader = gl.createShader(gl.VERTEX_SHADER); 
gl.shaderSource(vertexShader, document.getElementById('vertexShader').text); 
gl.compileShader(vertexShader);

var fragmentShader = gl.createShader(gl.FRAGMENT_SHADER); 
gl.shaderSource(fragmentShader, document.getElementById('fragmentShader').text); 
gl.compileShader(fragmentShader); 
```

There is a special program which connects two types of shaders:

```
var program = gl.createProgram(); 
gl.attachShader(program, vertexShader); 
gl.attachShader(program, fragmentShader); 
gl.linkProgram(program); 
```

If **uniform** variables are connected directly with JavaScript variables, then for attributes we should use one special thing - **buffer**. Buffer's data is stored in video card memory which incredibly improves the speed of rendering.

In this case we need:

1. Vertices buffer for storing information about our dots.
2. Colors buffer containing the info about colors we use.

Now we are going to define the first buffer:

![Vertices buffer](http://ipestov.com/content/images/2016/02/7e82b81e52b548719407bb80dc46e0bd.png)

_Our triangle geometry._

It has the following coordinates:

- (0, 0, 0).
- (0.5, 1, 0).
- (1, 0, 0).

Keep in mind that there are some specialities when working with buffers:

1. We provide data in one array without nesting so in case of our triangle it would look like this: [0, 0, 0, 0.5, 1, 0, 1, 0, 0].
2. Our input should only contain of [typed arrays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays).
3. Before sending, your have to define the type of buffer which you're going to use with `gl.bindBuffer`.

This is how it should look in your program:

```
var vertexBuffer = gl.createBuffer(); 
var vertices = [0, 0, 0, 0.5, 1, 0, 1, 0, 0]; 
gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer); 
gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(vertices), gl.STATIC_DRAW); 
```

Now we will create the second buffer with colors using the same method. We need to define the color for each vertex in RGB format where each component is between 0 and 1:

```
var colorBuffer = gl.createBuffer(); 
var colors = [1, 0, 0, 0, 1, 0, 0, 0, 1]; 
gl.bindBuffer(gl.ARRAY_BUFFER, colorBuffer); 
gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(colors), gl.STATIC_DRAW); 
```

Finally, we have to combine it with our shaders code and call the painting method:

```
// Get position of variables in shaders program
var uPosition = gl.getUniformLocation(program, 'u_position'); 
var aPosition = gl.getAttribLocation(program, 'a_position'); 
var aColor = gl.getAttribLocation(program, 'a_color');

// Set which shader program are we going to use
gl.useProgram(program);

// Give our uniform variable the coordinates
gl.uniform3fv(uPosition, [0, 0, 0]);

// Combine colors data
gl.bindBuffer(gl.ARRAY_BUFFER, colorBuffer); 
gl.enableVertexAttribArray(aColor); 
// The second argument to pass the dimension, RGB has 3 components
gl.vertexAttribPointer(aColor, 3, gl.FLOAT, false, 0, 0);

// And vertices
gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer); 
gl.enableVertexAttribArray(aPosition); 
gl.vertexAttribPointer(aPosition, 3, gl.FLOAT, false, 0, 0);

// Paint all around in white
gl.clearColor(1.0, 1.0, 1.0, 1.0); 
gl.clear(gl.COLOR_BUFFER_BIT);

// Paint the triangle
// The third argument is a number of vertices
gl.drawArrays(gl.TRIANGLES, 0, 3); 
```

So we're done:<br>
![Our triangle](http://ipestov.com/content/images/2016/02/bede8f313f2b43c2b271fb1d6531569f.png)

_You can see the full source [here](https://jsfiddle.net/Trufi/c11xno8j/)._

How I've said before, pixels colors inside the triangle linearly interpolated between the colored vertices. We've just painted the easiest shape using WebGL and found out what shaders are and how to work with buffers. Let's get further.

## How To Paint A Cube And Make It Rotating

Now let's make it harder and create a 3D cube. It will be made of six edges, each has two triangles.

![A cube](http://ipestov.com/content/images/2016/02/15c2b5127ee94e4eb462032e3aa1387f.png)

We will have to define coordinates for each vertex of each triangle. There are easier ways to do that but we will do it simple first:

```
var vertexBuffer = gl.createBuffer(); 
var vertices = [ 
 // Front edge
 -1, -1, -1,
 1, -1, -1,
 -1, -1, 1,

 1, -1, 1,
 -1, -1, 1,
 1, -1, -1,

 // Back edge
 -1, 1, -1,
 -1, 1, 1,
 1, 1, -1,

 1, 1, 1,
 1, 1, -1,
 -1, 1, 1,

 // Lower edge
 -1, -1, -1,
 -1, 1, -1,
 1, -1, -1,

 1, 1, -1,
 1, -1, -1,
 -1, 1, -1,

 // Upper edge
 -1, -1, 1,
 1, -1, 1,
 -1, 1, 1,

 1, 1, 1,
 -1, 1, 1,
 1, -1, 1,

 // Left edge
 -1, -1, -1,
 -1, -1, 1,
 -1, 1, -1,

 -1, 1, 1,
 -1, 1, -1,
 -1, -1, 1,

 // Right edge
 1, -1, -1,
 1, 1, -1,
 1, -1, 1,

 1, 1, 1,
 1, -1, 1,
 1, 1, -1
];
gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer); 
gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(vertices), gl.STATIC_DRAW); 
```

And the colors buffer:

1. (1, 0.5, 0.5).
2. (0.5, 0.7, 1).
3. (0.3, 1, 0.3).

Another easy way to do it:

```
var colorBuffer = gl.createBuffer(); 
var colors = [ 
 // Front edge
 1, 0.5, 0.5,
 1, 0.5, 0.5,
 1, 0.5, 0.5,
 1, 0.5, 0.5,
 1, 0.5, 0.5,
 1, 0.5, 0.5,

 // Back edge
 1, 0.5, 0.5,
 1, 0.5, 0.5,
 1, 0.5, 0.5,
 1, 0.5, 0.5,
 1, 0.5, 0.5,
 1, 0.5, 0.5,

 // Lower edge
 0.5, 0.7, 1,
 0.5, 0.7, 1,
 0.5, 0.7, 1,
 0.5, 0.7, 1,
 0.5, 0.7, 1,
 0.5, 0.7, 1,

 // Upper edge
 0.5, 0.7, 1,
 0.5, 0.7, 1,
 0.5, 0.7, 1,
 0.5, 0.7, 1,
 0.5, 0.7, 1,
 0.5, 0.7, 1,

 // Left edge
 0.3, 1, 0.3,
 0.3, 1, 0.3,
 0.3, 1, 0.3,
 0.3, 1, 0.3,
 0.3, 1, 0.3,
 0.3, 1, 0.3,

 // Right edge
 0.3, 1, 0.3,
 0.3, 1, 0.3,
 0.3, 1, 0.3,
 0.3, 1, 0.3,
 0.3, 1, 0.3,
 0.3, 1, 0.3
];
gl.bindBuffer(gl.ARRAY_BUFFER, colorBuffer); 
gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(colors), gl.STATIC_DRAW); 
```

The position of the triangle in space was set by the vector of third dimension. But the figure can not only change position, it can still be rotated and scaled. Therefore, in three-dimensional graphics is not used the vector, but the matrix.

It is known that the rotation matrix in three-dimensional space is defined by using a **3×3 matrix**. This position vector is added to the matrix, thus eventually uses **4×4 matrix**.

**WebGL does not help us to work with matrices**, therefore, not to spend a lot of time on them, we will use a fairly well-known library [glMatrix](http://glmatrix.net/). Let's create with it an identity matrix:

```
var cubeMatrix = mat4.create(); 
```

To draw a 3D object, we need to introduce the concept of the camera. The camera, as well as any object has its own position in space. It also determines which objects are visible on the screen, and is responsible for the conversion of the figures so that on the screen we create the **illusion of 3D**.

![The prospect of the cube on the screen](http://ipestov.com/content/images/2016/02/dce43bf2775e45b59838f9fba410871e.png)

_The prospect of the cube on the screen._

Perspective matrix is responsible during this transformation. With **glMatrix** it is created in two lines:

```
var cameraMatrix = mat4.create(); 
mat4.perspective(cameraMatrix, 0.785, window.innerWidth / window.innerHeight, 0.1, 1000); 
```

`mat4.perspective(matrix, fov, aspect, near, far)` takes 5 parameters:

- **matrix** -- the matrix you want to edit.
- **fov** -- viewing angle in radians.
- **aspect** -- the proportion of the sides.
- **near** -- minimum distance to the objects that will be visible.
- **far** -- maximum distance to the objects that will be visible.

To make cube image comes into the camera, move the camera on the Z axis:

```
mat4.translate(cameraMatrix, cameraMatrix, [0, 0, -5]); 
```

In contrast to the triangle in the cube are used shaders matrix position and camera matrix in addition:

Vertex shader:

```
attribute vec3 a_position; 
attribute vec3 a_color; 
uniform mat4 u_cube; 
uniform mat4 u_camera; 
varying vec3 v_color; 
void main(void) { 
 v_color = a_color;
 gl_Position = u_camera articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii u_cube articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii vec4(a_position, 1.0);
}
```

Fragmental shader:

```
precision mediump float; 
varying vec3 v_color; 
void main(void) { 
 gl_FragColor = vec4(v_color.rgb, 1.0);
}
```

Shaders Initialization occurs just as in the case of a triangle:

```
var vertexShader = gl.createShader(gl.VERTEX_SHADER); 
gl.shaderSource(vertexShader, document.getElementById('vertexShader').text); 
gl.compileShader(vertexShader);

var fragmentShader = gl.createShader(gl.FRAGMENT_SHADER); 
gl.shaderSource(fragmentShader, document.getElementById('fragmentShader').text); 
gl.compileShader(fragmentShader);

var program = gl.createProgram(); 
gl.attachShader(program, vertexShader); 
gl.attachShader(program, fragmentShader); 
gl.linkProgram(program);

var uCube = gl.getUniformLocation(program, 'u_cube'); 
var uCamera = gl.getUniformLocation(program, 'u_camera'); 
var aPosition = gl.getAttribLocation(program, 'a_position'); 
var aColor = gl.getAttribLocation(program, 'a_color'); 
```

To cube is not standing still, and to make it rotated, it is necessary to constantly change its position and to update the frame. Updating occurs using `requestAnimationFrame` function.

Unlike other similar methods, `requestAnimationFrame` calls the passed function only when the video card is free and ready for rendering the next frame.

```
// Create the identity matrix cube position
var cubeMatrix = mat4.create();

// Let us remember the last frame rendering
var lastRenderTime = Date.now();

function render() { 
 // Requesting rendering the next frame
 requestAnimationFrame(render);

 // Get the time elapsed since the last frame
 var time = Date.now();
 var dt = lastRenderTime - time;

 // Rotate the cube relative to the Y axis
 mat4.rotateY(cubeMatrix, cubeMatrix, dt / 1000);
 // Rotate the cube relative to the Z axis
 mat4.rotateZ(cubeMatrix, cubeMatrix, dt / 1000);

 // Paint all around in white
 gl.clearColor(1.0, 1.0, 1.0, 1.0);
 gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

 // Enable filter depth
 gl.enable(gl.DEPTH_TEST);

 gl.useProgram(program);

 gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
 gl.enableVertexAttribArray(aPosition);
 gl.vertexAttribPointer(aPosition, 3, gl.FLOAT, false, 0, 0);

 gl.bindBuffer(gl.ARRAY_BUFFER, colorBuffer);
 gl.enableVertexAttribArray(aColor);
 gl.vertexAttribPointer(aColor, 3, gl.FLOAT, false, 0, 0);

 gl.uniformMatrix4fv(uCube, false, cubeMatrix);
 gl.uniformMatrix4fv(uCamera, false, cameraMatrix);

 gl.drawArrays(gl.TRIANGLES, 0, 36);

 lastRenderTime = time;
}

render(); 
```

Get a spinning cube:<br>
![A spinning cube](http://ipestov.com/content/images/2016/02/18f45fe19dae45639cf96c63d0d2341c.png)

_You can see the full source code [here](https://jsfiddle.net/Trufi/r36cw20t/)._

We learned how to draw a simple cube, understood how to get it rotating and got acquainted with the concepts of the matrix and the position of the camera.

## How To Debug

Since part of the program which works with WebGL is executed on the side of video card, the debugging process is much more complicated.<br>
No familiar tools as **DevTools** and even **console.log**.<br>
On the internet there are a lot of articles and papers, but here I give the basics.

To understand that the shader code was written with an error, the following method can be used when compiling the shader:

```
if (!gl.getShaderParameter(vertexShader, gl.COMPILE_STATUS)) { 
 console.log(gl.getShaderInfoLog(vertexShader));
}

if (!gl.getShaderParameter(fragmentShader, gl.COMPILE_STATUS)) { 
 console.log(gl.getShaderInfoLog(fragmentShader));
}

if (!gl.getProgramParameter(program, gl.LINK_STATUS)) { 
 console.log('Could not initialize shaders');
}
```

There is also a special extension for the browser [WebGL-Inspector](https://benvanik.github.io/WebGL-Inspector/). It allows you to keep track of loaded shaders, buffers, textures, graphics card and calls of WebGL methods.

Still there is Shader Editor, in Firefox Dev Tools this functionality built already in and there is a Chrome [extension](https://github.com/spite/ShaderEditorExtension) that allows you to edit shader code directly in the application.

## Where To Go

In this article I have tried to highlight the main points which may cause difficulties while studying WebGL. Despite the fact that different vectors are used, projection and matrixes, to know how things work inside is optionally. WebGL is a great tool for a multitude of tasks, and you can use it not only in game development. Do not be afraid to try something new, to discover new technologies and experiment.

Finally - a list of useful resources where you can continue to explore WebGL.

- The full source of [triangle](https://jsfiddle.net/Trufi/c11xno8j/) and [cube](https://jsfiddle.net/Trufi/r36cw20t/).
- Summary of WebGL from Kronos Group website.
- For a more detailed study it is recommended to take a series of WebGL Learning lessons.
- Free course on the basics of 3D on [Udacity](https://www.udacity.com/course/interactive-3d-graphics--cs291). Although they use three.js library, it will be useful to all.
- Report by Vladimir Agafonkin about [WebGL and Mapbox](https://www.youtube.com/watch?v=yMmyzzApGy4) at Frontend Dev Conf.
- [Slides of the report](https://docs.google.com/presentation/d/12AGAUmElB0oOBgbEEBfhABkIMCL3CUX7kdAPLuwZ964/edit#slide=id.i8) "Debugging and Optimizing WebGL Applications".

**Original article was written by great 2Gis team on [Habrahabr](http://habrahabr.ru/company/2gis/blog/273735/)**.
