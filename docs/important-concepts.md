# Important shader concepts

## The space between 0 and 1

When writing shaders everything you need to do exists in the space between 0 and 1!
This might sound confusing, but it is actually really smart, because it makes our calculations incredible fast and easy to deal with. As we will see in a moment when we create a gradient (already we are moving beyond what [**fill()**](https://p5js.org/reference/#/p5/fill) can do!).

* **Colors** are defined as RGB but the color values goes from 0 - 1 instead of 0 - 255. 
  * So the color Blue would be (0.0, 0.0, 1.0) instead of (0, 0, 255).
  * And the color Orange would be (1.0, 0.5, 0.0) instead of (255, 128, 0).

* **Position** of the pixels on your canvas is defined in terms of their position between 0 and 1.
  * So position x = 0, y = 0 or (0, 0) for a sketch, is position (0.0, 0.0) for the shader.
  * Position x = width, y = height or (width, height) for a sketch, is position (1.0, 1.0) for the shader.

*This means, that as far as your shader is concerned, your canvas has a size of 1, going from (0.0, 0.0) in the lower left corner to (1.0, 1.0) in the upper right corner!*

^^^^^^^^^^^^^check this.

------------>>>>>>>>> ( Interactive? Drawing of Canvas Scale 0 to 1 )


## Floating point values are important
Since everything is defined between 0 and 1. Always write out the whole floating point value, including the punctuation! Do not write (0,0,1) for blue, write (0.0, 0.0, 1.0). Graphics cards can be more or less picky about this, but if you have a picky graphics card, the shader will not run!

_________________________________________


## Making and changing variables

By now you will have noticed that we are using vectors (vec3, vec4 etc.) a lot in shaders. We use them to enter in our numbers for different variables. This is simply how we write our variables, just like you might know types such as integers and floats from other programming languages.

We need to tell the variable how many numbers they will contain, so we use these types: float, vec2, vec3, vec4, vec5 etc. The number is the amount of parameters you intend to use. 

```glsl
float scalar = 5.2; 			// a scalar that could be used to manipulate color or position

vec2 position = vec2(0.5, 0.5);   	// the center of the canvas (width/2, height/2)

vec3 color = vec3(0.0, 0.5, 1.0); 	// R = 0, G = 0.5, B = 1.0

vec4 colorWithAlpha = vec4(0.0, 0.0, 1.0, 0.5); 	// blue color that is half transparent A = 0.5

```


What is neat about constructing our variables like this, is that we can access their components very easily.

We can access these as color.r, color.g, color.b:
```glsl
vec3 color = vec3(0.0, 0.5, 1.0); 	// R = 0, G = 0.5, B = 1.0

float redComponent = color.r; 		// R = 0
float greenComponent = color.g; 	// G = 0.5
float blueComponent = color.r; 		// B = 1.0

```

If we write color.rgb we get all three.
We could even write color.xyz, the shader does not know that some numbers are a color and some are a position, it just knows that you are accesing the components of a vector. This is called [**Swizzling**](https://www.khronos.org/opengl/wiki/Data_Type_(GLSL)#Swizzling).

```glsl
vec3 color = vec3(0.0, 0.5, 1.0); 	// R = 0, G = 0.5, B = 1.0

vec3 colorCopy1 = color.rgb; 		// R = 0, G = 0.5, B = 1.0
vec3 colorCopy2 = color.xyz; 		// R = 0, G = 0.5, B = 1.0

```


We can change them just as easily:

```glsl
vec3 color = vec3(0.0, 0.5, 1.0); 	// R = 0, G = 0.5, B = 1.0

color.r = 0.833; 			// setting the R component to a new value

vec3 newColor = color;			// R = 0.833, G = 0.5, B = 1.0

```

------------>>>>>>>>> 

## Variable Qualifiers
Variable qualifiers go before the variable type and name to determine how the variable will be used.
### Using Const or #define
Const is short for Constant, which is a variable that never changes in the life of our program.
Unlike p5, constants like PI are not native to shader code. So in order to use PI in our code we have to define the constant PI in our code.
There are two ways of doing this:
```glsl
const float PI = 3.14159265358979323846;
```
or
```glsl
#define PI 3.14159265358979323846
```
Using #define is different than our const float variable in that it is not a variable but rather a script that is run before our shader compiles to replace all mentions of PI with the specified number. This is theoretically faster and more efficient because it is not a variable stored in memory.

### Using Attributes
Attributes are per-vertex parameters, as a result of this they are only used in our vertex file (.vert). They are typically used to recieve vertex data from p5, which is then converted to a usable format and passed to our fragment shader.

Our vert code:
```glsl
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

### Using Uniforms
Uniforms are constant variables, but unlike consts they are constant per frame, which means that they can be updated between draw calls. Uniforms can be accessed by all of the parallel threads in our GPU (remember the Mona Lisa painted by pipes example). They are called uniforms because the information being received by each thread is the same, as a result of this necessity of uniformity for all threads, each thread can read the input data but cannot modify it.

The important thing to know about uniforms is that they are how we can pass information from the CPU to GPU, or in other words, from p5 to our shader code.

The most common uniforms to pass from p5 are time, resolution, and mouse coordinates. It is common practice to name these with a u_ prefix.

Our p5 code to send those 3 uniforms:
```javascript
theShader.setUniform("u_resolution", [width, height]);
theShader.setUniform("u_time", millis() / 1000.0); // we divide millis by 1000 to convert it to seconds
theShader.setUniform("u_mouse", [mouseX, map(mouseY, 0, height, height, 0)]); // we flip Y so it's oriented properly in our shader
```
Then in our fragment shader (.frag) we must recieve the uniforms and define their type (vec2, float, etc.):
```glsl
uniform vec2 u_resolution;
uniform float u_time;
uniform vec2 u_mouse;
```

### Using Varying
Varying are per-fragment (per-pixel) parameters. They vary from pixel to pixel, unlike uniforms which are same for all pixels.
Varying are typically used to pass texture coordinates that we get from WEBGL/p5 through our vertex shader to our fragment shader.
Our vert code:
```glsl
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
```glsl
// grab our textures coordinates from vert shader
varying vec2 vTexCoord;

void main(){
  //set our uv coordinates to our texture coordinates
  vec2 uv = vTexCoord;
}
```




Now that we know that every position is defined as a floating point number between 0 and 1, and we know about uniforms, let's put it to work!




# Shader Code: Gradient fill

[https://gradient-color.glitch.me](https://gradient-color.glitch.me)

The following code makes a gradient depending on the position of the pixel on your canvas. The vertex shader file is the same as always.



### shader.vert file

```glsl

#ifdef GL_ES

precision mediump float;

#endif


attribute vec3 aPosition;


// Always include this to get the position of the pixel and map the shader correctly onto the shape

void main() {

  vec4 positionVec4 = vec4(aPosition, 1.0);

  positionVec4.xy = positionVec4.xy * 2.0 - 1.0;

  // Send the vertex information on to the fragment shader

  gl_Position = positionVec4;

}

```


We pass in the size of the canvas as a uniform called **vec2 u_resolution**.
This is set up in the sketch.js file: theShader.setUniform("u_resolution", [width, height]);

The uniform now contains the width and height of the canvas, with u_resolution.x containing the width, and u_resolution.y containing the height.


```glsl
uniform vec2 u_resolution;
```

In the **main()** function we get the position of each pixel on our canvas from **gl_FragCoord.xy** (fragment coordinate). This automaticall uses the information we provided in **gl_Position** in the vertex shader file.

By dividing the x,y position of the pixel (gl_FragCoord.xy) by the width,height of the canvas (u_resolution.xy)
we get normalized coordinates in the range: 0.0-1.0! 
So st.x is going from 0 to 1 in the x-axis, and st.y is going from 0 to 1 in the y-axis.

It is a standard convention when writing shaders to call this variable **st**.

If were are going to use the positions of the pixels at all in our shaders, we should make it a habit to start our shaders by doing this.

```glsl
vec2 st = gl_FragCoord.xy/u_resolution.xy; 
```

We end our code by setting the color of the pixel **gl_FragColor**, as always (remember this must always be done or else your code won't run). 

We can now make a gradient from black to red in the x-axis, if we use the **st.x** variable as input.

```glsl
// R = dependent on pixel location in x-axis, from 0 to 1, G = 0, B = 0, A = 1
gl_FragColor = vec4(st.x,0.0,0.0,1.0); 


```

You could also make the green color dependent on the pixel location in the x-axis.
Or use both the st.x and st.y to decide the color of the pixel.


```glsl
// R = 0, G = dependent on pixel location in x-axis, from 0 to 1, B = 0, A = 1
gl_FragColor = vec4(0.0,st.x,0.0,1.0); 

// R = dependent on pixel location in x-axis, G = dependent on pixel location in y-axis, B = 0, A = 1
gl_FragColor = vec4(st.x,st.y,0.0,1.0); 
```

*NOTE: You can only have one gl_FragColor active at a time, so make sure you are only setting it once.*



### shader.frag file

```glsl

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
  // try the green channel

  //gl_FragColor = vec4(0.0,st.x,0.0,1.0); 


  // try both the x position and the y position
  
  //gl_FragColor = vec4(st.x,st.y,0.0,1.0); 

}

```
