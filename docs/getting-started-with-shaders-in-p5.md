# Getting started with shaders in p5

Let's look at a pared down example of a shader in a P5 sketch so we can begin to understand what's going on. We'll look at the p5.js code first.

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

We also specify noStroke() for this example so our shader is borderless.

```javascript
function setup() {

createCanvas(200,200,WEBGL);

}
```

Now in draw we must set our active shader using the built in shader() function, and create a p5 rect for the shader to be applied to (you can apply shaders to any shape).

```javascript
function draw() {

  shader(theShader);

  rect(0,0,width,height);

}
```

And that's it for the p5 code!

___________________________________________

# p5.js Code

```javascript

// a shader variable

let theShader;

function preload(){

  // load the shader

  theShader = loadShader('onecolor.vert', 'onecolor.frag');

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
