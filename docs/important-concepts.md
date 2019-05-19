# Important shader concepts

On this page you will find a collection of the most "need to know" information to get started with shaders, so you'll be ready to dive into the collection of p5 examples.

## The space between 0 and 1

When writing shaders everything you need to do exists in the space between 0 and 1!
This might sound confusing, but it is actually really smart, because it makes our calculations incredible fast and easy to deal with. As we will see in a moment when we create a gradient (already we are moving beyond what [**fill()**](https://p5js.org/reference/#/p5/fill) can do!).

* **Colors** are defined as RGB but the color values goes from 0 - 1 instead of 0 - 255. 
  * So the color Blue would be (0.0, 0.0, 1.0) instead of (0, 0, 255).
  * And the color Orange would be (1.0, 0.5, 0.0) instead of (255, 128, 0).

* **Position** of the pixels on your canvas is defined in terms of their position between 0 and 1.
  * So position x = 0, y = 0 or (0, 0) for a sketch, is position (0.0, 0.0) for the shader.
  * Position x = width, y = height or (width, height) for a sketch, is position (1.0, 1.0) for the shader.

*This means, that as far as your shader is concerned, your canvas has a size of 1, going from (0.0, 0.0) in the lower left corner to (1.0, 1.0) in the upper right corner! This is contrary to the p5 canvas, which has (0,0) in the upper left corner, and (width,height) in the bottom right corner. So do not get confused. *

In this example, as you move your mouse you can see the pixel location (white box) and the normalized location (blackbox) of your cursor:

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/normal-coord-vs-normalized?path=uniform.frag&previewSize=100&sidebarCollapsed=true"
    alt="normal-coord-vs-normalized on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

## Floating point values are important
Since everything is defined between 0 and 1. Always write out the whole floating point value, including the punctuation! Do not write (0,0,1) for blue, write (0.0, 0.0, 1.0). Graphics cards can be more or less picky about this, but if you have a picky graphics card, the shader will not run!

_________________________________________


## Making and changing variables

It is important to note that GLSL (our shader language) is strongly typed. What this means is that we have to define the type of all of our variables (int, float, vec2, etc.). This is different from Javascript where we simply say ***let myVariable*** and it figures out what type it is from the data we store in it. This also means we can't change the type of data a variable holds on the fly like in Javascript, or pass an int when a function is expecting a float.

At first it will be confusing to have to type **vec2 position** or **float scale** for every variable but you'll get used to it in no time.

### The wonders of vectors

By now you will have noticed that we are using vectors (vec3, vec4 etc.) a lot in shaders. Vectors are used when we need a variable to hold two or more numbers.  If we only need the variable to hold one number we use float or int, otherwise we say vec2, vec3, vec4, vec5 etc. The number is the amount of parameters you intend to use. 

In math vectors are used to positions or directions, but in shader land vectors are also used to store colors.

```gfrag
float scalar = 5.2; 			// a scalar that could be used to manipulate color or position

vec2 position = vec2(0.5, 0.5);   	// the center of the canvas (width/2, height/2)

vec3 color = vec3(0.0, 0.5, 1.0); 	// R = 0, G = 0.5, B = 1.0

vec4 colorWithAlpha = vec4(0.0, 0.0, 1.0, 0.5); 	// blue color that is half transparent A = 0.5

```

What is neat about constructing our variables like this, is that we can access their components very easily.

We can access these as color.r, color.g, color.b:
```frag
vec3 color = vec3(0.0, 0.5, 1.0); 	// R = 0, G = 0.5, B = 1.0

float redComponent = color.r; 		// R = 0
float greenComponent = color.g; 	// G = 0.5
float blueComponent = color.r; 		// B = 1.0

```

If we write color.rgb we get all three. 

We could even write color.xyz, the shader does not know that some numbers are a color and some are a position, it just knows that you are accesing the components of a vector. However it is good practive to write .rgba if your variable is a color, and .xyzw if it is a position.

This "dot" way of accessing components is called [**Swizzling**](https://www.khronos.org/opengl/wiki/Data_Type_(GLSL)#Swizzling).

```frag
vec3 color = vec3(0.0, 0.5, 1.0); 	// R = 0, G = 0.5, B = 1.0

vec3 colorCopy1 = color.rgb; 		// R = 0, G = 0.5, B = 1.0
vec3 colorCopy2 = color.xyz; 		// R = 0, G = 0.5, B = 1.0

```


We can change them just as easily:

```frag
vec3 color = vec3(0.0, 0.5, 1.0); 	// R = 0, G = 0.5, B = 1.0

color.r = 0.833; 			// setting the R component to a new value

vec3 newColor = color;			// R = 0.833, G = 0.5, B = 1.0

```

## Making functions in shaders

You can make functions in the .vert or .frag file easily. You need to define what type (for instance vec3) you expect to get back from the function. 

Our .frag code:

```frag
// a function that transforms the rgb values we pass in to the space between 0 and 1
vec3 rgb(float r, float g, float b){
  return vec3(r / 255.0, g / 255.0, b / 255.0);
}

void main() {
  // red color
  vec3 color = rgb(255.0, 0.0, 0.0)
  gl_FragColor = vec4(color, 1.0);
}

```

## Texture Coordinates
TexCoord and FragCoord—you may have noticed these variables in your shader code but not understood exactly what they meant. Think about your shader as a stretchy cloth and these coord variables as a series of pins that hold the corners tight. Depending on the location and spacing of these pins your shader can be stretched, distorted, or duplicated in different ways.

TexCoord is calculated in the vertex and passed from the vertex to the fragment shader, and then the texture coordinates of our shader are set to be equal to TexCoord.

.vert
```vert
// our texcoordinates from the cpu
attribute vec2 aTexCoord;

// this is a variable that will be shared with the fragment shader
// we will assign the attribute texcoords to the varying texcoords to move them from the vert shader to the frag shader
// it can be called whatever you want but often people prefix it with 'v' to indicate that it is a varying
varying vec2 vTexCoord;

void main() {

  // copy the texture coordinates
  vTexCoord = aTexCoord;
  
}
```

.frag
```frag
   // recieve vTexCoord from vertex shader
   varying vec2 vTexCoord;
  
  // vTexCoord is a value that goes from 0.0 - 1.0 depending on the pixels location
  // we can use it to access every pixel on the screen
  vec2 st = vTexCoord;
```

FragCoord is used within the fragment shader to calculate the texture coordinates relative to the resolution uniform we passed it.<br>
.frag
```glsl
void main (void) {
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
 }
```

You may see coordinates referred as uv, st, coord, etc. these are just different naming conventions for the same thing.

***

## Variable Qualifiers
You can further define how a variable can be used by using variable qualifiers. Variable qualifiers go before the variable type (float, vec2 etc).

### Variable qualifiers: Using Const or #define
Const is short for "Constant", which is a variable that never changes in the life of our program. You cannot overwrite a const after you have created it.

Unlike p5, constants like PI are not native to shader code. So in order to use PI in our code we have to define the constant PI in our code.

There are two ways of doing this:
```frag
const float PI = 3.14159265358979323846;
```
or
```frag
#define PI 3.14159265358979323846
```
Using #define is different than our const float variable in that it is not a variable but rather a script that is run before our shader compiles to replace all mentions of PI with the specified number. In coding this # syntax is known as a "preprocessor directive". This is theoretically faster and more efficient because it is not a variable stored in memory. 

### Variable qualifiers: Using Attributes
Attributes are per-vertex parameters, as a result of this they are only used in our vertex file (.vert). They are typically used to recieve vertex data from p5 shapes, which is then converted to a usable format and passed to our fragment shader.

We have already used an attribute when we map the shader onto the shape. Remember our .vert code:
```vert
// our vertex data from WEBGL/p5
attribute vec3 aPosition;

void main() {
  // copy the position data into a vec4
  // we're using 1.0 as the w component (which controls scaling/normalization of the coordinates)
  vec4 positionVec4 = vec4(aPosition, 1.0);
  positionVec4.xy = positionVec4.xy * 2.0 - 1.0;

  // send the vertex information on to the fragment shader
  gl_Position = positionVec4;
}
```

### Variable qualifiers: Using Uniforms
Uniforms are constant variables, but unlike consts they are constant *per frame*, which means that they can be updated between draw calls. Uniforms can be accessed by all of the parallel threads in our GPU ([remember the Mona Lisa painted by pipes example](https://itp-xstory.github.io/p5js-shaders/#/./docs/what-are-shaders?id=how-does-a-shader-work)). They are called uniforms because the information being received by each thread is the same, as a result of this necessity of uniformity for all threads, each thread can read the input data but cannot modify it.

The important thing to know about uniforms is that they are how we can pass information from the CPU to GPU, or in other words, from p5 to our shader code.

The most common uniforms to pass from p5 are time, resolution, and mouse coordinates. It is common practice to name these with a "u_" or "u" prefix.

Our p5 code to send those 3 uniforms:
```javascript
theShader.setUniform("u_resolution", [width, height]);
theShader.setUniform("u_time", millis() / 1000.0); // we divide millis by 1000 to convert it to seconds
theShader.setUniform("u_mouse", [mouseX, map(mouseY, 0, height, height, 0)]); // we flip Y so it's oriented properly in our shader
```
Then in our fragment shader (.frag) we must recieve the uniforms and define their type (vec2, float, etc.):
```frag
uniform vec2 u_resolution;
uniform float u_time;
uniform vec2 u_mouse;
```

### Variable qualifiers: Using Varying
Varying are per-fragment (per-pixel) parameters. They vary from pixel to pixel, unlike uniforms which are same for all pixels.
Varying are typically used to pass texture coordinates that we get from WEBGL/p5 through our vertex shader to our fragment shader. It is common practive to prefix these with a "v" or "v_" when naming the variable.

Our vert code:
```vert
// I'm excluding our normal vertex position data operations from this example code.
// get our texture coordinates from WEBGL/p5
attribute vec2 aTexCoord;
// create a varying vec2 which will store our texture coordinates
varying vec2 vTexCoord;

void main() {
  // copy the texcoords
  vTexCoord = aTexCoord;
 }
```
Our frag code:
```frag
// grab our textures coordinates from vert shader
varying vec2 vTexCoord;

void main(){
  //set our uv coordinates to our texture coordinates
  vec2 uv = vTexCoord;
}
```



# Shader Code: Gradient fill

The following code makes a gradient depending on the position of the pixel on your canvas.

Let's use some of the knowledge from above and try to experiment.

We will use the knowledge that:
* position on the canvas is defined as a floating point number between 0 and 1
* uniforms can be used to pass information from our p5 sketch.js to our shader


<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/gradient-color?path=uniform.frag&previewSize=100"
    alt="gradient-color on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>



### shader.vert file
The vertex shader file is the same as [always](https://itp-xstory.github.io/p5js-shaders/#/./docs/how-to-write-a-shader?id=content-of-a-shadervert-file).

```vert
#ifdef GL_ES
precision mediump float;
#endif

attribute vec3 aPosition;

// Always include this to get the position of the pixel and map the shader correctly onto the shape

void main() {

  // Copy the position data into a vec4, adding 1.0 as the w parameter
  vec4 positionVec4 = vec4(aPosition, 1.0);

  // Scale to make the output fit the canvas
  positionVec4.xy = positionVec4.xy * 2.0 - 1.0; 

  // Send the vertex information on to the fragment shader
  gl_Position = positionVec4;
}

```

### shader.frag file
We pass in the size of the canvas as a uniform called **vec2 u_resolution**.
This is set up in the sketch.js file: theShader.setUniform("u_resolution", [width, height]);

The uniform now contains the width and height of the canvas, with u_resolution.x containing the width, and u_resolution.y containing the height.


```frag
uniform vec2 u_resolution;
```

In the **main()** function we get the position of each pixel on our canvas from **gl_FragCoord.xy** (fragment coordinate). This automaticall uses the information we provided in **gl_Position** in the vertex shader file.

By dividing the x,y position of the pixel (gl_FragCoord.xy) by the width,height of the canvas (u_resolution.xy)
we get normalized coordinates in the range: 0.0-1.0! 
So st.x is going from 0 to 1 in the x-axis, and st.y is going from 0 to 1 in the y-axis.

It is a standard convention when writing shaders to call this variable **st**.

If were are going to use the positions of the pixels at all in our shaders, we should make it a habit to start our shaders by doing this.

```frag
vec2 st = gl_FragCoord.xy/u_resolution.xy; 
```

We end our code by setting the color of the pixel **gl_FragColor**, as always (remember this must always be done or else your code won't run). 

We can now make a gradient from black to red in the x-axis, if we use the **st.x** variable as input.

```frag
// R = dependent on pixel location in x-axis, from 0 to 1, G = 0, B = 0, A = 1
gl_FragColor = vec4(st.x,0.0,0.0,1.0); 
```

You could also make the green color dependent on the pixel location in the x-axis.
Or use both the st.x and st.y to decide the color of the pixel. *NOTE: You can only have one gl_FragColor active at a time, so make sure you are only setting it once.*


```frag
// R = 0, G = dependent on pixel location in x-axis, from 0 to 1, B = 0, A = 1
gl_FragColor = vec4(0.0,st.x,0.0,1.0); 

// R = dependent on pixel location in x-axis, G = dependent on pixel location in y-axis, B = 0, A = 1
gl_FragColor = vec4(st.x,st.y,0.0,1.0); 
```

The complete shader.frag file looks like this:

```frag
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution; // This is passed in as a uniform from the sketch.js file

void main() {

  // position of the pixel divided by resolution, to get normalized positions on the canvas
  vec2 st = gl_FragCoord.xy/u_resolution.xy; 

  // Lets use the pixels position on the x-axis as our gradient for the red color
  // Where the position is closer to 0.0 we get black (st.x = 0.0)
  // Where the position is closer to width (defined as 1.0) we get red (st.x = 1.0)

  gl_FragColor = vec4(st.x,0.0,0.0,1.0); // R,G,B,A

  // you can only have one gl_FragColor active at a time, but try commenting the others out
  // try the green component

  //gl_FragColor = vec4(0.0,st.x,0.0,1.0); 

  // try both the x position and the y position
  
  //gl_FragColor = vec4(st.x,st.y,0.0,1.0); 
}
```
