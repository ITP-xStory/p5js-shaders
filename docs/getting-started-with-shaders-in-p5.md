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
