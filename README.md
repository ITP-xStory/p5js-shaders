<!----- Conversion time: 2.951 seconds.


Using this Markdown file:

1. Cut and paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β16
* Sat Apr 06 2019 06:44:45 GMT-0700 (PDT)
* Source doc: https://docs.google.com/a/nyu.edu/open?id=111IJVUH3x4krBFI1dtERPpumVfVCL8HQxlvLKDwpJAo

WARNING:
You have 8 H1 headings. You may want to use the "H1 -> H2" option to demote all headings by one level.

----->


<p style="color: red; font-weight: bold">>>>>>  gd2md-html alert:  ERRORs: 0; WARNINGs: 1; ALERTS: 0.</p>
<ul style="color: red; font-weight: bold"><li>See top comment block for details on ERRORs and WARNINGs. <li>In the converted Markdown or HTML, search for inline alerts that start with >>>>>  gd2md-html alert:  for specific instances that need correction.</ul>

<p style="color: red; font-weight: bold">Links to alert messages:</p>
<p style="color: red; font-weight: bold">>>>>> PLEASE check and correct alert issues and delete this message and the inline alerts.<hr></p>



# Getting started with shaders \
using p5.js

This will live as a wiki page on p5js github: [https://github.com/processing/p5.js/wiki](https://github.com/processing/p5.js/wiki) 


# 


# Overview / to write

_The following is an overview of the Reference/tutorial we are writing for p5.js including a list of the examples that must be made. \
We are trying to bridge the gap for a programmer who just started programming (p5 community) to understand how to make shaders._

_ _--------------------------------------------------------------------------------------------

**Using shaders with p5**



*   A soft introduction to shaders, getting started
*   What are they
*   How to use a shader
    *   The space between 0 and 1
    *   Making variables and changing them
*   The power of shaders
*   When to use them with p5
*   Good resources for learning more about shaders on the web
    *   The book of shaders
    *   … etc.
    *   Link to example on how to load shaders from these sources
    *   A list of common words found in shader literature
        *   BoS Related: pct, st
        *   Swizzling
        *   Uniforms
        *   Shaping Functions
        *   Rendering
        *   Pixel
        *   Texel

**Using shaders with p5 (examples)**



*   Loading a shader onto a canvas
*   Loading several shaders onto a canvas / several canvas?
*   Loading a shader onto a 3D object / 3D primitive

--------------------------------------------------------------------------------------------

**Interactivity with shaders**



*   Reminder: Uniforms
*   Etc.

**Interactivity with shaders (examples)**



*   Mouse x,y
*   Keys for events (draw a shape / trigger a wave)
*   Sliders

--------------------------------------------------------------------------------------------

**Using the camera in your shaders**



*   Adam Ferris examples...

--------------------------------------------------------------------------------------------

**Setting up shaders from external sources**



*   load shaders from ___ in P5.
    *   Book of Shaders
    *   ShaderToy javascript files

--------------------------------------------------------------------------------------------

**More shader examples (resource list)**



*   Adam Ferris has many great examples


# 


[TOC]



# 


# The Reference


## Getting started with shaders using p5

Shaders are a great way to make graphics for your projects. So far you have probably made graphic shapes like **rect**() or **ellipse**() or maybe even 3D shapes like a **plane**() or a **box**(), and you should be more than familiar with using **fill**() to decide the color of these objects. But wouldn't it be great if we could make these objects have moving graphics on them? And without slowing down our sketch?

Enter the shader!

(IMG different examples of shaders on stuff in p5)


## What is a shader?

First of all, even though you may never have written a shader before you have definitely come across them! Every 3D object in every computer game ever has a shader attached to the it, the shader decides what color the object takes, but also how light bounces off the object, whether it is transparent and more. Also, if you have ever been to a concert where there were visuals which reacted to the sound or movements, these were likely shaders! 

A shader is a small program that runs entirely on your graphics card, the GPU (Graphics Processing Unit), rather than the CPU (Central Processing Unit) of your computer. This makes it incredible fast.

While your CPU works by doing every calculation - every move of a pixel or shape you have been drawing until now - in sequence, meaning it takes each one and thinks about it, one a at time - your GPU is actually capable of something called parallel processing.

This means that everything you draw in your sketch by using a shader is drawn all at once, using the GPU! Therefor shaders are an incredible optimized way of making more complex drawings, without slowing your sketch down.

To understand what a shader does, imagine the CPU as a single pipe, where all of the pixels on your canvas must pass through, one at a time, to be told what color they should have. Now imagine instead, that each pixel has its own pipe, and we could just tell every pixels on your canvas what color it should have in parallel (at the same time). That is what a shader does. 

It is kind of like painting the Mona Lisa with a blast canon.

[https://www.youtube.com/watch?v=-P28LKWTzrI](https://www.youtube.com/watch?v=-P28LKWTzrI) 


## How do we use a shader?


## HOW TO SET IT UP IN P5



*   The simplest setup! \
Do not introduce uniforms yet! See my comment further down "search gradient"

Let's look at a pared down example of a shader in a P5 sketch so we can begin to understand what's going on. We'll look at the p5.js code first.

First we create a variable that will hold our shader in our p5 sketch.

**let theShader;**

Then we have to preload our shader files and store them in our shader variable, so that they are loaded before the sketch runs its setup or draw functions. Our shaders require two files, more on that later.

**function preload(){**

**  theShader = loadShader('shader.vert', 'shader.frag');**

**}**

Now in setup we create our canvas. Shaders in p5.js are rendered using WebGL, so we must make sure to specify WEBGL as the third parameter in the createCanvas() function.

We also specify noStroke() for this example so our shader is borderless.

**function setup() {**

**createCanvas(200,200,WEBGL);**

**}**

Now in draw we must set our active shader using the built in shader() function, and create a p5 rect for the shader to be applied to (you can apply shaders to any shape).

**function draw() {**

**  shader(theShader);**

**  rect(0,0,width,height);**

**}**

And that's it for the p5 code!

___________________________________________

**Sketch.js**

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

___________________________________________

Now let's talk about what these shader files we're loading are and what they do.

To make a shader you must make two files in your p5 sketch folder: A "shader.vert" file and a "shader.frag" file.

You could name the shader files whatever you want. "myAwesomeShader.vert" and "myAwesomeShader.frag" are completely valid names. As long as you have both the .vert and the .frag file you are good to go. 

It's important to note that these files are written in GLSL (OpenGL Shading Language), which is a lower level language than Javascript, meaning it speaks more directly to our computer, specifically to our GPU. The code will seem foreign and confusing at first but with time we can develop more of an understanding of what is going on.

The .vert file handles everything that has to do with vertexes - that is all of your geometry and its position on the canvas. The .frag file handles everything that has to do with the actual coloring of the pixels.

The programs run like this:

The .vert file is run first, and automatically passes the calculations we did about the geometry on our canvas on to the .frag program. The fragment shader then colors the pixels according to their positions!

_The important thing you need to know is that the content of these two files will apply to all pixels! These two programs are run simultaneously for every single pixel on you canvas. _

Let us look at a simple example: 

___________________________________________

**EXAMPLE:**

**Coloring your background using a shader **

**Writing our own version of fill()**

[https://glitch.com/~one-color](https://glitch.com/~one-color) 

___________________________________________

**Content of shader.vert file**

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

**Content of shader.frag file**

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

______________________________________________


### Get used to thinking about the space between 0 and 1!

When writing shaders everything you need to do exists in the space between 0 and 1!

This might sound confusing, but it is actually really smart, because it makes our calculations incredible fast and easy to deal with. As we will see in a moment when we create a gradient (already we are moving beyond what fill() can do!).

Colors are defined as RGB but the color values goes from 0 - 1 instead of 0 - 255. 

So the color Blue would be (0.0, 0.0, 1.0) instead of (0, 0, 255).

And the color Orange would be (1.0, 0.5, 0.0) instead of (255, 128, 0).

The position of the pixels on your canvas is defined in terms of their position between 0 and 1, so position x = 0, y = 0 or (0, 0) for a sketch, is position (0.0, 0.0) for the shader (note the floating point values). 

Position x = width, y = height or (width, height) for a sketch, is position (1.0, 1.0) for the shader.

This means, that as far as your shader is concerned, your canvas has a size of 1, going from (0.0, 0.0) in the lower left corner to (1.0, 1.0) in the upper right corner!

DRAWING OF CANVAS SCALE 0 to 1

Since everything is defined between 0 and 1. Always write out the whole floating point value, including the punctuation! Do not write (0,0,1) for blue, write (0.0, 0.0, 1.0). This is because different graphics cards are more or less piggy, and if you have a piggy graphics card, the shader will not run!


### Making variables and changing them

By now you will have noticed that we are using vectors a lot in shaders. We use them to enter in our numbers for different variables. So for now just get used to writing your numbers this way.

We need to tell the variable how many numbers they will contain, so we use types: float, vec2, vec3, vec4, vec5 etc. The number is the amount of parameters you intend to use. 

float scalar = 5.2; // for instance: a scalar that could manipulate the color in some way

vec2 position = vec2(0.5, 0.5);   // for instance: the center of the canvas (width/2, height/2)

vec3 color = vec3(0.0, 0.5, 1.0); // for instance: R = 0, G = 0.5, B = 1.0

vec4 colorWithAlpha = vec4(0.0, 0.5, 1.0, 0.5); // for instance: color that is half transparent A = 0.5

The neat way about constructing our variables this way is the we can access their components easily.

We can access these as color.r, color.g, color.b

If we write color.rgb we get all three!

(We could also do color.xyz, it'd be the same.)

This is called Swizzling.

Uniforms

Write about normalization

Now that we know that every position is defined as a float between 0 and 1 we can make a gradient, and we know about uniforms, let's put it to work!



___________________________________________

**EXAMPLE:**

**Coloring your background using a shader **

**Writing a fill() that is a gradient**

[https://gradient-color.glitch.me](https://gradient-color.glitch.me)

___________________________________________

**Content of shader.vert file**

// These are necessary definitions that let you graphics card know how to render the shader

#ifdef GL_ES

precision mediump float;

#endif

attribute vec3 aPosition;

// Always include this to get the position of the pixel

void main() {

  vec4 positionVec4 = vec4(aPosition, 1.0);

  positionVec4.xy = positionVec4.xy * 2.0 - 1.0;

  // Send the vertex information on to the fragment shader

  gl_Position = positionVec4;

}

**Content of shader.frag file**

// These are necessary definitions that let you graphics card know how to render the shader

#ifdef GL_ES

precision mediump float;

#endif

// In this example we care about where on the canvas the pixel lives, so we need to know the size of the canvas.

// This is passed in as a uniform from the sketch.js file.

uniform vec2 u_resolution;

void main() {

  /*

  We get the position of each pixel on our canvas from gl_FragCoord.xy (a built in shader function).

  

     By dividing the x,y position of the pixel (gl_FragCoord.xy) by the width,height of the canvas (u_resolution.xy)

     we get normalized coordinates in the range: 0.0-1.0!

     Meaning st.x and st.y goes from 0.0 in the lower left corner to 1.0 in the upper right corner.

     We always start our shaders by doing this.

  */

	vec2 st = gl_FragCoord.xy/u_resolution.xy;

  // Lets use the pixels position on the x-axis as our gradient for the red color

  // Where the position is closer to 0.0 we get black,

  // Where the postiion is closer to width (defined as 1.0) we get red 

  gl_FragColor = vec4(st.x,0.0,0.0,1.0); // R,G,B,A

  

  // you can only have one gl_FragColor active at a time, but try commenting the others out

  // try the green channel

  //gl_FragColor = vec4(0.0,st.x,0.0,1.0); 

  

  // try both the x position and the y position

  //gl_FragColor = vec4(st.x,st.y,0.0,1.0); 

}

______________________________________________


## The power of shaders

Alright, so far we have made a fill() function and a gradient function similar to lerpColor(). But the true power of shaders is only really revealed when you are trying to make oven more complex coloring. 

You might not understand this example right now, but you will see how a shader works and how fast it runs even though we are putting some pretty complex graphics in your sketch.

______________________________________________

So try this out:

EXAMPLE THAT IS REALLY COMPLEX! LIKE A MANDELBROT OR SOMETHING. \
Just to show power of shaders.

______________________________________________


## When to use a shader in p5?

You should be looking into shader

And when not to ...


## Learn more about shaders

There are some amazing resources on the web for learning more about shaders.

We hope that this introduction has given you the basics for learning more…

**List of resources:**

[https://thebookofshaders.com](https://thebookofshaders.com)

The book of shaders is an incredible online book with interactive examples, made by [Patricio Gonzalez Vivo](http://patriciogonzalezvivo.com/) and [Jen Lowe](http://jenlowe.net/). It is by far the best place to start learning more about shaders.


# 


## Examples


### Using shaders with p5 (examples)



*   Loading a shader onto a canvas
*   Create graphics
*   Loading several shaders onto a canvas / several canvas?
*   Loading a shader onto a 3D object / 3D primitive


### Interactivity with shaders


### Interactivity with shaders (examples)


### Using the camera in your shaders


### Setting up shaders from external sources


### More shader examples (resource list)


# 


# Re-write of the documentation pages on p5js.org

[https://p5js.org/reference/](https://p5js.org/reference/)


```


### p5.Shader
```



#### **Description**

Shader class for WEBGL Mode


#### **Methods**


<table>
  <tr>
   <td><code><a href="https://p5js.org/reference/#/p5.Shader/setUniform">setUniform()</a></code>
   </td>
   <td>Wrapper around gl.uniform functions. As we store uniform info in the shader we can use that to do type checking on the supplied data and call the appropriate function.
   </td>
  </tr>
</table>



```


### loadShader()
```



#### **Example**

Examples for loadShader

loadShader example 1


```
let mandel;
function preload() {
  // load the shader definitions from files
  mandel = loadShader('assets/shader.vert', 'assets/shader.frag');
}
function setup() {
  createCanvas(100, 100, WEBGL);
  // use the shader
  shader(mandel);
  noStroke();
  mandel.setUniform('p', [-0.74364388703, 0.13182590421]);
}

function draw() {
  mandel.setUniform('r', 1.5 * exp(-6.5 * (1 + sin(millis() / 2000))));
  quad(-1, -1, 1, -1, 1, 1, -1, 1);
}
```



#### **Description**

Loads a custom shader from the provided vertex and fragment shader paths. The shader files are loaded asynchronously in the background, so this method should be used in <code>[preload()](https://p5js.org/reference/#/p5/preload)</code>.

For now, there are three main types of shaders. p5 will automatically supply appropriate vertices, normals, colors, and lighting attributes if the parameters defined in the shader match the names.


#### **Syntax**


#### **Parameters**


<table>
  <tr>
   <td><code>vertFilename</code>
   </td>
   <td>String: 
<p>
path to file containing vertex shader source code
   </td>
  </tr>
  <tr>
   <td><code>fragFilename</code>
   </td>
   <td>String: 
<p>
path to file containing fragment shader source code
   </td>
  </tr>
</table>



#### **Returns**

p5.Shader: a shader object created from the provided vertex and fragment shader files.


<!-- Docs to Markdown version 1.0β16 -->
