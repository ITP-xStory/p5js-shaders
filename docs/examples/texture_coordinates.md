# Texture Coordinates
### This is an example showig how texture coordinates can be manipulated to duplicate variations of one image.

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/moire-shader?path=uniform.frag&previewSize=100"
    alt="moire-shader-2 on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

***

#### js file
```javascript
// a shader variable
let theShader;

function preload(){
  // load the shader
  theShader = loadShader('uniform.vert', 'uniform.frag');
}

function setup() {
  // shaders require WEBGL mode to work
  createCanvas(windowWidth, windowHeight, WEBGL);
  noStroke();
}

function draw() {
  // shader() sets the active shader with our shader
  shader(theShader);

  // here we're using setUniform() to send our uniform values to the shader
  // set uniform is smart enough to figure out what kind of variable we are sending it,
  // so there's no need to cast (unlike processing)
  theShader.setUniform("u_resolution", [width, height]);
  theShader.setUniform("u_time", millis() / 1000.0);

  // rect gives us some geometry on the screen
  rect(0,0,width,height);
}
```
#### frag file
```frag
// rotate/tile functions from example by patricio gonzalez vivo
// @patriciogv ( patriciogonzalezvivo.com )

#ifdef GL_ES
precision mediump float;
#endif

#define PI 3.14159265358979323846

uniform vec2 u_resolution;
uniform float u_time;


vec2 rotate2D (vec2 _st, float _angle) {
    _st -= 0.5;
    _st =  mat2(cos(_angle),-sin(_angle),
                sin(_angle),cos(_angle)) * _st;
    _st += 0.5;
    return _st;
}

vec2 tile (vec2 _st, float _zoom) {
    _st *= _zoom;
    return fract(_st);
}

vec2 rotateTilePattern(vec2 _st){

    //  Scale the coordinate system by 2x2
    _st *= 2.0;

    //  Give each cell an index number
    //  according to its position
    float index = 0.0;
    index += step(1., mod(_st.x,2.0));
    index += step(1., mod(_st.y,2.0))*2.0;

    //      |
    //  2   |   3
    //      |
    //--------------
    //      |
    //  0   |   1
    //      |

    // Make each cell between 0.0 - 1.0
    _st = fract(_st);

    // Rotate each cell according to the index
    if(index == 1.0){
        //  Rotate cell 1 by 90 degrees
        _st = rotate2D(_st,PI*0.5);
    } else if(index == 2.0){
        //  Rotate cell 2 by -90 degrees
        _st = rotate2D(_st,PI*-0.5);
    } else if(index == 3.0){
        //  Rotate cell 3 by 180 degrees
        _st = rotate2D(_st,PI);
    }

    return _st;
}

float concentricCircles(in vec2 st, in vec2 radius, in float resolution, in float scale) {
    float dist = distance(st,radius);
    float pct = floor(dist*resolution)/scale;
    return pct;
}

void main (void) {
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
	// st = rotateTilePattern(st);
    float dist = distance(st,vec2(abs(sin(u_time/10.0)),abs(cos(u_time/10.0))));
    st = tile(st,4.0);
    st = rotateTilePattern(st);

    // Make more interesting combinations
    // st = tile(st,dist *(sin(u_time) +1.0)* 10.0);
    st = rotate2D(st,dist*20.);
    // st = rotateTilePattern(st*2.);
    // st = rotate2D(st,PI*u_time*0.25);

    // step(st.x,st.y) just makes a b&w triangles
    // but you can use whatever design you want.
    gl_FragColor = vec4(vec3(concentricCircles(st, vec2(0.0,0.0), 5.0, 10.0)),1.0);
}
```
#### vert file
```vert
// vert file and comments from adam ferriss
// https://github.com/aferriss/p5jsShaderExamples

// our vertex data
attribute vec3 aPosition;

void main() {

  // copy the position data into a vec4, using 1.0 as the w component
  vec4 positionVec4 = vec4(aPosition, 1.0);
  positionVec4.xy = positionVec4.xy * 2.0 - 1.0;

  // send the vertex information on to the fragment shader
  gl_Position = positionVec4;
}
```
