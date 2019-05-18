# Porting Shadertoy Code to p5.js Shaders

Shadertoy is an excellent resource for getting inspiration for your shader code.
Browsing through the examples you may see a noise function, for example, that would be useful in a sketch you're working on.
Shadertoy has some real master shader alchemists and we can become their apprentices by sifting through their formulas and equations,
just make sure however to give proper attribution and pay attention to any licensing specified by the author.

***

### u_uniform vs iUniform
In shadertoy code your vert code is handled for you, as well as having many included shader inputs (uniforms). These uniforms are identified by an "i" prefix, ex: iMouse. A big part of using converting shadertoy code to p5.js is figuring out and sending each uniform the data that it needs.

To save myself the hassle of converting all the uniforms names in the shadertoy code, I simply pass it the uniforms the way it's named in the code:

```javascript
  theShader.setUniform("iResolution", [width, height]);
  theShader.setUniform("iFrame", frameCount);
  theShader.setUniform("iMouse", [mouseX, map(mouseY, 0, height, height, 0)]);
```

and dont forget to receive them at the top of the frag file:

```glsl
uniform vec2 iResolution;
uniform int iFrame;
uniform vec2 iMouse;
```
After that we just have to change fragCoord to gl_FragCoord, and fragColor to gl_FragColor.
In the shadertoy:
```glsl
vec2 uv = fragCoord/iResolution.xy;
fragColor = vec4(col,1.0);
```
In our frag file:
```glsl
// in the shadertoy fragcoord in a vec2 so we have to only pass the first 2 values
vec2 uv = gl_FragCoord.xy/iResolution.xy;
gl_FragColor = vec4(col,1.0);
```

The last thing is the difference in how void main called in shadertoy vs straight GLSL.
In the shadertoy:
```glsl
void mainImage( out vec4 fragColor, in vec2 fragCoord ) {
// all the beautiful shader code
}
```
In our frag file:
```
void main(){
// all the beautiful shader code
}
```
In our frag we don't have to specify that gl_FragColor is going out and gl_FragCoord is coming in, and we use the prefix "gl_" which is convention for built in variables.

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/recursive-noise-experiment?path=shader.frag&previewSize=100"
    alt="recursive-noise-experiment on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

***

### Example:
#### [Recursive Noise Experiment by ompuco](https://www.shadertoy.com/view/wllGzr)
#### Original Shadertoy Code:
```glsl
float hash( float n )
			{
			    return fract(sin(n)*43758.5453);
			}

			float noise( vec3 x )
			{
			    // The noise function returns a value in the range -1.0f -> 1.0f

			    vec3 p = floor(x);
			    vec3 f = fract(x);

			    f       = f*f*(3.0-2.0*f);
			    float n = p.x + p.y*57.0 + 113.0*p.z;

			    return mix(mix(mix( hash(n+0.0), hash(n+1.0),f.x),
			                   mix( hash(n+57.0), hash(n+58.0),f.x),f.y),
			               mix(mix( hash(n+113.0), hash(n+114.0),f.x),
			                   mix( hash(n+170.0), hash(n+171.0),f.x),f.y),f.z)-.5;
			}


void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    
    
    vec3 t = (float(iFrame)*vec3(1.0,2.0,3.0)/1.0)/1000.0;//+iMouse.xyz/1000.0;

    
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    uv=uv/4.0+.5;
    uv-=iMouse.xy/iResolution.xy/4.0;

    vec3 col = vec3(0.0);
    
    
    
    for(int i = 0; i < 16; i++){
        float i2 = float(i)*1.0;
					col.r+=noise(uv.xyy*(12.0+i2)+col.rgb+t*sign(sin(i2/3.0)));
					col.g+=noise(uv.xyx*(12.0+i2)+col.rgb+t*sign(sin(i2/3.0)));
					col.b+=noise(uv.yyx*(12.0+i2)+col.rgb+t*sign(sin(i2/3.0)));
				}
				

				for(int i = 0; i < 16; i++){
                    float i2 = float(i)*1.0;
					col.r+=noise(uv.xyy*(32.0)+col.rgb+t*sign(sin(i2/3.0)));
					col.g+=noise(uv.xyx*(32.0)+col.rgb+t*sign(sin(i2/3.0)));
					col.b+=noise(uv.yyx*(32.0)+col.rgb+t*sign(sin(i2/3.0)));
				}
				col.rgb/=32.0;
				col.rgb=mix(col.rgb,normalize(col.rgb)*2.0,1.0);
				col.rgb+=.3;
    
    

    // Output to screen
    fragColor = vec4(col,1.0);
}
```
### p5.js code version:
#### .js file:
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
  
  theShader.setUniform("iResolution", [width, height]);
  theShader.setUniform("iFrame", frameCount);
  theShader.setUniform("iMouse", [mouseX, map(mouseY, 0, height, height, 0)]);

  // rect gives us some geometry on the screen
  rect(0,0,width, height);
}

function windowResized(){
  resizeCanvas(windowWidth, windowHeight);
}
```
#### .frag file:
```glsl
// this is a port of "recursive noise experiment" by ompuco
// https://www.shadertoy.com/view/wllGzr

#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 iResolution;
uniform int iFrame;
uniform vec2 iMouse;

float hash( float n )
			{
			    return fract(sin(n)*43758.5453);
			}

			float noise( vec3 x )
			{
			    // The noise function returns a value in the range -1.0f -> 1.0f

			    vec3 p = floor(x);
			    vec3 f = fract(x);

			    f       = f*f*(3.0-2.0*f);
			    float n = p.x + p.y*57.0 + 113.0*p.z;

			    return mix(mix(mix( hash(n+0.0), hash(n+1.0),f.x),
			                   mix( hash(n+57.0), hash(n+58.0),f.x),f.y),
			               mix(mix( hash(n+113.0), hash(n+114.0),f.x),
			                   mix( hash(n+170.0), hash(n+171.0),f.x),f.y),f.z)-.5;
			}


void main()
{
    
    
    vec3 t = (float(iFrame)*vec3(1.0,2.0,3.0)/1.0)/1000.0;//+iMouse.xyz/1000.0;

    
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = gl_FragCoord.xy/iResolution.xy;
    uv=uv/4.0+.5;
    uv-=iMouse.xy/iResolution.xy/4.0;

    vec3 col = vec3(0.0);
    
    
    
    for(int i = 0; i < 16; i++){
        float i2 = float(i)*1.0;
					col.r+=noise(uv.xyy*(12.0+i2)+col.rgb+t*sign(sin(i2/3.0)));
					col.g+=noise(uv.xyx*(12.0+i2)+col.rgb+t*sign(sin(i2/3.0)));
					col.b+=noise(uv.yyx*(12.0+i2)+col.rgb+t*sign(sin(i2/3.0)));
				}
				

	 for(int i = 0; i < 16; i++){
        float i2 = float(i)*1.0;
					col.r+=noise(uv.xyy*(32.0)+col.rgb+t*sign(sin(i2/3.0)));
					col.g+=noise(uv.xyx*(32.0)+col.rgb+t*sign(sin(i2/3.0)));
					col.b+=noise(uv.yyx*(32.0)+col.rgb+t*sign(sin(i2/3.0)));
				}
				col.rgb/=32.0;
				col.rgb=mix(col.rgb,normalize(col.rgb)*2.0,1.0);
				col.rgb+=.3;
    
    

    // Output to screen
    gl_FragColor = vec4(col,1.0);
}
```
#### .vert file:
```glsl
// our vertex data
attribute vec3 aPosition;

// our texcoordinates
attribute vec2 aTexCoord;

void main() {

  // copy the position data into a vec4, using 1.0 as the w component
  vec4 positionVec4 = vec4(aPosition, 1.0);
  positionVec4.xy = positionVec4.xy * 2.0 - 1.0;

  // send the vertex information on to the fragment shader
  gl_Position = positionVec4;
}
```
