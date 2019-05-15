# Important shader concepts

### Get used to thinking about the space between 0 and 1!

When writing shaders everything you need to do exists in the space between 0 and 1!
This might sound confusing, but it is actually really smart, because it makes our calculations incredible fast and easy to deal with. As we will see in a moment when we create a gradient (already we are moving beyond what [**fill()**](https://p5js.org/reference/#/p5/fill) can do!).

* **Colors** are defined as RGB but the color values goes from 0 - 1 instead of 0 - 255. 
⋅⋅* So the color Blue would be (0.0, 0.0, 1.0) instead of (0, 0, 255).
⋅⋅* And the color Orange would be (1.0, 0.5, 0.0) instead of (255, 128, 0).

* **Position** of the pixels on your canvas is defined in terms of their position between 0 and 1.
⋅⋅* So position x = 0, y = 0 or (0, 0) for a sketch, is position (0.0, 0.0) for the shader (note the floating point values).
⋅⋅* Position x = width, y = height or (width, height) for a sketch, is position (1.0, 1.0) for the shader.

*This means, that as far as your shader is concerned, your canvas has a size of 1, going from (0.0, 0.0) in the lower left corner to (1.0, 1.0) in the upper right corner!*

^^^^^^^^^^^^^check this.

------------>>>>>>>>> ( Drawing of Canvas Scale 0 to 1 )


### Get used to writing out everything as floating point values
Since everything is defined between 0 and 1. Always write out the whole floating point value, including the punctuation! Do not write (0,0,1) for blue, write (0.0, 0.0, 1.0). Graphics cards can be more or less picky about this, but if you have a picky graphics card, the shader will not run!

------------>>>>>>>>>  Now that we know that every position is defined as a float between 0 and 1 we can make a gradient, and we know about uniforms, let's put it to work!


# Making variables and changing them

By now you will have noticed that we are using vectors (vec3, vec4 etc.) a lot in shaders. We use them to enter in our numbers for different variables. This is simply how we write our variables, just like you might know types such as integers and floats from other programming languages.

We need to tell the variable how many numbers they will contain, so we use these types: float, vec2, vec3, vec4, vec5 etc. The number is the amount of parameters you intend to use. 

```glsl
float scalar = 5.2; 	// a scalar that could manipulate the color in some way

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

If we write color.rgb we get all three (we could even write color.xyz, it'd be the same, the shader does not care, you are just accesing the components.)
```glsl
vec3 color = vec3(0.0, 0.5, 1.0); 	// R = 0, G = 0.5, B = 1.0

vec3 colorCopy1 = color.rgb; 		// R = 0, G = 0.5, B = 1.0
vec3 colorCopy2 = color.xyz; 		// R = 0, G = 0.5, B = 1.0

```

This is called **Swizzling**. You can read more about it here if you are interested: https://www.khronos.org/opengl/wiki/Data_Type_(GLSL)#Swizzling.



#

**EXAMPLE:**

**Coloring your background using a shader**

**Writing a fill() that is a gradient**

[https://gradient-color.glitch.me](https://gradient-color.glitch.me)
#

**Content of shader.vert file**

```glsl

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

```

**Content of shader.frag file**

```glsl

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

```

1. Uniform
2. Attributes
3. Other common variables



## The power of shaders

Alright, so far we have made a fill() function and a gradient function similar to lerpColor(). But the true power of shaders is only really revealed when you are trying to make oven more complex coloring. 

You might not understand this example right now, but you will see how a shader works and how fast it runs even though we are putting some pretty complex graphics in your sketch.

#

So try this out:

EXAMPLE THAT IS REALLY COMPLEX! LIKE A MANDELBROT OR SOMETHING. \
Just to show power of shaders.

#


## When to use a shader in p5?

You should be looking into shader

And when not to ...
