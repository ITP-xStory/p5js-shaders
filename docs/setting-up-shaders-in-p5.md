# Setting up shaders in p5.js

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

## p5.js code

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

# Setting up shaders in the p5.js Editor


Initially when we wrote this guide, shader files were not supported in the p5.js editor, but after a big overhaul from Cassie Tarakajian @catarak it now does!

Here is our basic example, set up in the editor and shown here using iframe (under the share menu):

<iframe src="https://preview.p5js.org/louiselessel/embed/xCPxmXGVE"></iframe>

And here is the example in the editor 
https://editor.p5js.org/louiselessel/sketches/xCPxmXGVE

You simply click 'Create File' on the 'Sketch Files' to the left (if it's hidden click that arrow above your code!) and then create a file for both the .vert and .frag code.


___________________________________________

# Setting up p5.js in Glitch


All of the examples in this guide are set up using Glitch.com. An awesome platform to host your code online with custom URLs, make collections, collaborate in teams and more. 
We use it because it is an easy way to get you going and you'll be able to view and remix all of our examples.

To get started, visit our [glitch template page](https://glitch.com/edit/#!/shader-template) and click "Remix to Edit" in the top right corner.

![](https://github.com/ITP-xStory/p5js-shaders/blob/gh-pages/assets/shader_template_remix.png?raw=true)

You'll immediately be able to start making your own p5.js / shader sketch. You'll need to make an account if you want to save your sketch, just click sign in at the top right and make an account. Logging through Github or other accounts is also an option.

![](https://github.com/ITP-xStory/p5js-shaders/blob/gh-pages/assets/shader_template_login.png?raw=true)

After signing up and logging in, you can click "remix" on any example in this guide, and they will get saved to your Glitch account! If you are not logged in, you can still click "remix" and change the code, but the code won't be saved once you leave this page.

We encourage you to feel free to experiment with the examples as you go through the guide!



