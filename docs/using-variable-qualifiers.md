# Using Variable Qualifiers
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
