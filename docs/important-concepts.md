# Important shader concepts

On this page you will find a collection of the most "need to know" information to get started with shaders, so you'll be ready to dive into the collection of p5 examples.

## The space between 0 and 1

When writing shaders everything you need to do exists in the space between 0 and 1!
This might sound confusing, but it is actually really smart, because it makes our calculations incredible fast and easy to deal with. As we will see on the bottom on this page when we create a gradient shader (already we are moving beyond what [**fill()**](https://p5js.org/reference/#/p5/fill) can do!).

* **Colors** are defined as RGB but the color values goes from 0 - 1 instead of 0 - 255. 
  * So the color Blue would be (0.0, 0.0, 1.0) instead of (0, 0, 255).
  * And the color Orange would be (1.0, 0.5, 0.0) instead of (255, 128, 0).

* **Position** of the pixels on your canvas is defined in terms of their position between 0 and 1.
  * So position x = 0, y = 0 or (0, 0) for a sketch, is position (0.0, 0.0) for the shader.
  * Position x = width, y = height or (width, height) for a sketch, is position (1.0, 1.0) for the shader.

*This means, that as far as your shader is concerned, your canvas has a size of 1, going from (0.0, 0.0) in the lower left corner to (1.0, 1.0) in the upper right corner! This is contrary to the p5 canvas, which has (0,0) in the upper left corner, and (width,height) in the bottom right corner. So do not get confused.*

In this example, as you move your mouse you can see the pixel location (white box) and the normalized (0 to 1) location (blackbox) of your cursor:

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/normal-coord-vs-normalized?path=uniform.frag&previewSize=100&sidebarCollapsed=true"
    alt="normal-coord-vs-normalized on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>


In the example we include this IMPORTANT line in our sketch.js file to make sure that the scaling is correct from 0 to 1. 
Some displays (retina for instance) scale pixels differently. So if you do not include this line (try removing it to test your display) you might not have a 0 to 1 space to work with. 

You would notice this easily if you output gl_FragColor = vec4(st.x,st.y,0.0,1.0); We should see black in lower left corner, and yellow in the upper right corner. Everything else should be a gradient with red in bottom right, and green in top left. 

Without the line you might have yellow already in the middle of the sketch area! This means we reached a color of r,g,b,a = 1.0, 1.0, 0.0, 1.0 way too soon!

```javascript
  // disables scaling for retina screens which can create inconsistent scaling between displays
  pixelDensity(1);
```

## Floating point values are important
Since everything is defined between 0 and 1. Always write out the whole floating point value, including the punctuation! Do not write (0,0,1) for blue, write (0.0, 0.0, 1.0). Graphics cards can be more or less picky about this, but if you have a picky graphics card, the shader will not run!

_________________________________________


## Making and changing variables

It is important to note that GLSL (our shader language) is strongly typed. What this means is that we have to define the type of all of our variables (int, float, vec2, etc.). This is different from Javascript where we simply say ***let myVariable*** and it figures out what type it is from the data we store in it. This also means we can't change the type of data a variable holds on the fly like in Javascript, or pass an int when a function is expecting a float.

At first it will be confusing to have to type **vec2 position** or **float scale** for every variable but you'll get used to it in no time.

### The wonders of vectors

By now you will have noticed that we are using vectors (vec3, vec4 etc.) a lot in shaders. Vectors are used when we need a variable to hold two or more numbers.  If we only need the variable to hold one number we use float or int, otherwise we say vec2, vec3, vec4, etc. The number is the amount of parameters you intend to use. 

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

Here is an example of a function you can place in the .frag file.

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


***
