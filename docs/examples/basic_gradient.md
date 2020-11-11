# Basic Gradient Shader based on pixel coordinates

The following code makes a gradient depending on the position of the pixel on your canvas.

Let's use some of the knowledge we have learned so far and try to experiment.

We will use the knowledge that:
* [position on the canvas is defined as a floating point number between 0 and 1](https://itp-xstory.github.io/p5js-shaders/#/./docs/important-concepts?id=the-space-between-0-and-1)
* [uniforms can be used to pass information from our p5 sketch.js to our shader](https://itp-xstory.github.io/p5js-shaders/#/./docs/using-variable-qualifiers?id=variable-qualifiers-using-uniforms)


<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/gradient-color?path=uniform.frag&previewSize=100"
    alt="gradient-color on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

#### .js file
```javascript
// a shader variable
let theShader;

function preload(){
  // load the shader
  theShader = loadShader('basic.vert', 'basic.frag');
}

function setup() {
  // shaders require WEBGL mode to work
  createCanvas(windowWidth, windowHeight, WEBGL);
  noStroke();
}

function draw() {  
  // send resolution of sketch into shader
  theShader.setUniform('u_resolution', [width, height]);

  // shader() sets the active shader with our shader
  shader(theShader);

  // rect gives us some geometry on the screen
  rect(0,0,width, height);
}

function windowResized(){
  resizeCanvas(windowWidth, windowHeight);
}
```

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

In the **main()** function we get the position of each pixel on our canvas from **gl_FragCoord.xy** (fragment coordinate). This automatically uses the information we provided in **gl_Position** in the vertex shader file.

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
