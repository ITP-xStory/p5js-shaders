# Getting started with shaders in p5

Now that we know what a shader is and how it is different from how you write a p5 sketch, let's look at a simple example of a shader in p5, so we can start writing our own!

We'll look at the p5.js code first.

First we create a variable that will hold our shader in our p5 sketch.

```javascript
let theShader;
```

Then we have to preload our shader files and store them in our shader variable, so that they are loaded before the sketch runs its setup or draw functions. Our shaders require two files, more on that later.

```javascript
function preload(){

theShader = loadShader('shader.vert', 'shader.frag');

}
```

Now in setup we create our canvas. Shaders in p5.js are rendered using WebGL, so we must make sure to specify WEBGL as the third parameter in the createCanvas() function.

We also specify [**noStroke()**](https://p5js.org/reference/#/p5/noStroke) for this example so our shader is borderless.

```javascript
function setup() {

createCanvas(200,200,WEBGL);

noStroke();

}
```

Now in draw we must set our active shader using the built in [**shader()**](https://p5js.org/reference/#/p5/shader) function, and create a p5 rect for the shader to be applied to (you can apply shaders to any shape and the shader won't work unless you specify a shape in your code).

```javascript
function draw() {

  shader(theShader);

  rect(0,0,width,height);

}
```

And that's it for the p5 code! Now you know how to load a shader into p5. Next we will look at how shaders are written.

___________________________________________

# p5.js code

```javascript

// a shader variable

let theShader;

function preload(){

  // load the shader

  theShader = loadShader('shader.vert', 'shader.frag');

}

function setup() {

  // shaders require WEBGL mode to work

  createCanvas(windowWidth, windowHeight, WEBGL);

  noStroke();

}

function draw() {

  // shader() sets the active shader with our shader

  shader(theShader);

  // rect gives us some geometry on the screen

  rect(0,0,width,height);

  

  // print out the framerate

  //  print(frameRate());

}

function windowResized(){

  resizeCanvas(windowWidth, windowHeight);

}

```

___________________________________________

# How to write a shader

Now let's talk about what shader files we're loading.

To make a shader you must make two files in your p5 sketch folder: A **"shader.vert"** file and a **"shader.frag"** file.

You can name the shader files whatever you want. "myAwesomeShader.vert" and "myAwesomeShader.frag" are completely valid names. As long as you have both the .vert and the .frag file, you are good to go. 

It's important to note that these files are written in GLSL (OpenGL Shading Language), which is a lower level language than Javascript, meaning it speaks more directly to your computer, specifically to your GPU. The code will seem foreign and confusing at first, but with time we can develop more of an understanding of what is going on.

The .vert file handles everything that has to do with vertexes - that is all of your geometry (shapes) and its position on the canvas. The .frag file handles everything that has to do with the actual coloring of the pixels.

The programs run like this:

The .vert file is run first, and automatically passes the calculations we can about the geometry (shapes) on our canvas on to the .frag program. The fragment shader then colors the pixels according to their positions!

**The important thing you need to remember now is that the content of these two files will apply to all pixels! These two programs are run simultaneously for every single pixel on your canvas. As we [explained before](https://itp-xstory.github.io/p5js-shaders/#/./docs/what-are-shaders). **

Let us look at a simple example: 

___________________________________________

**EXAMPLE:**

**Coloring your background using a shader**

**Writing our own version of fill()**

[https://glitch.com/~one-color](https://glitch.com/~one-color) 

___________________________________________

**Content of shader.vert file**

```glsl

// These are necessary definitions that let you graphics card know how to render the shader

#ifdef GL_ES

precision mediump float;

#endif

// this is an attribute sent to the shader by p5 

// it contains all of our vertex position information

// it is a vec3, meaning it contains x, y, and z data

// attribute signals that this is a global variable sent by the sketch

// it is read only, meaning it cannot be changed directly (you can copy it though)

// attributes exist in vertex shaders only

attribute vec3 aPosition;

// This "vec3 aPosition" is a built in shader function. You must keep that naming.

// It figures out the position of the pixel on our canvas

attribute vec3 aPosition;

// We always must do at least this one thing in the vertex shader:

// Tell the pixels where on the canvas they live.

//  All shaders have a void main() function.

void main() {

  // Copy the position data into a vec4, adding 1.0 as the w parameter,

  //  when w = 1.0 the vector is treated as a position

  //  when w = 0.0 the vector is treated as a direction

  vec4 positionVec4 = vec4(aPosition, 1.0);

  // scale the rect by two, and move it to the center of the screen

  // if we don't do this, it will appear with its bottom left corner in the center of the sketch

  // try commenting this line out to see what happens

  positionVec4.xy = positionVec4.xy * 2.0 - 1.0;

  // The vertex shader requires there to be a vec4 output called gl_Position

  //  It sends the vertex information on to the fragment shader

  // this is done automatically, as long as you put it into the built in shader function "gl_Position"

  gl_Position = positionVec4;

}

```

**Content of shader.frag file**

```glsl

// These are necessary definitions that let you graphics card know how to render the shader

#ifdef GL_ES

precision mediump float;

#endif

// We are not going to do anything with the position data in this example, because we are coloring everything blue, so we are actually not using anything from our .vert program. However the position calculation still needs to be there.

void main() {

    // Make a blue color

    // In shaders, the RGB color goes from 0 - 1 instead of 0 - 255

    vec3 color = vec3(0.0, 0.0, 1.0);

    // gl_FragColor is a built in shader variable, and you .frag file must contain it

    // We are setting the vec3 color into a new vec4, with an transparency of 1 (no opacity)

    gl_FragColor = vec4(color, 1.0);

}

```
