# Applying Shaders to Vertices
#### This is an example showing how to apply your shaders to a vertex shape in p5.
It is necessary to pass uv values to the vertex so it knows how the texture should be mapped, in this case the shape changes, so the uv values are mapped to change with the shape.

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/shader-on-vertex?path=uniform.frag&previewSize=100"
    alt="shader-on-vertex on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

***

#### .js file
```javascript
// a shader variable
let theShader;
let shaderBg;

let x;
let y;
let outsideRadius = 200;
let insideRadius = 100;


function preload(){
  // load the shader
  theShader = loadShader('uniform.vert', 'uniform.frag');
}

function setup() {
  // shaders require WEBGL mode to work
  createCanvas(windowWidth, windowHeight, WEBGL);
  noStroke();
  
  // initialize the createGraphics layers
  shaderBg = createGraphics(windowWidth, windowHeight, WEBGL);
  
  // turn off the cg layers stroke
  shaderBg.noStroke();
  
   x = 0;
   y = 0;
}

function draw() {
  
  // shader() sets the active shader with our shader
  // instead of just setting the active shader we are passing it to the shaderBg graphic
  shaderBg.shader(theShader);

  // here we're using setUniform() to send our uniform values to the shader
  // set uniform is smart enough to figure out what kind of variable we are sending it,
  // so there's no need to cast (unlike processing)
  theShader.setUniform("u_resolution", [width, height]);
  theShader.setUniform("u_time", millis() / 1000.0);
  theShader.setUniform("u_mouse", [mouseX, map(mouseY, 0, height, height, 0)]);

  // rect gives us some geometry on the screen
  // passing the shaderBg graphic geometry to render on
  shaderBg.rect(0,0,width,height);
  
  background(255);
  texture(shaderBg);
  
  push();
  
  let numPoints = int(map(mouseX, 0, width, 3, 30));
  let angle = 0;
  let angleStep = 180.0 / numPoints;
  
  console.log(numPoints);

  beginShape(TRIANGLE_STRIP);
  for (let i = 0; i <= numPoints; i++) {
    let px = x + cos(radians(angle)) * outsideRadius;
    let py = y + sin(radians(angle)) * outsideRadius;
    
    let u1, v1, u2, v2;
    // console.log(px, -250, 250, 0, 1);
    
    u1 = map(px, -250, 250, 0, 1);
    v1 = map(py, -250, 250, 0, 1);
    
    angle += angleStep;
    vertex(px, py, u1, v1);
    px = x + cos(radians(angle)) * insideRadius;
    py = y + sin(radians(angle)) * insideRadius;
    
    u2 = map(px, -250, 250, 0, 1);
    v2 = map(py, -250, 250, 0, 1);
    
    vertex(px, py, u2, v2);
    angle += angleStep;
  }
  endShape();
  pop();
}
```
#### .frag file
```frag
// casey conchinha - @kcconch ( https://github.com/kcconch )
// more p5.js + shader examples: https://itp-xstory.github.io/p5js-shaders/
// rotate/tile functions from example by patricio gonzalez vivo
// @patriciogv ( patriciogonzalezvivo.com )

#ifdef GL_ES
precision mediump float;
#endif

#define PI 3.14159265358979323846

uniform vec2 u_resolution;
uniform float u_time;
uniform vec2 u_mouse;


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
    vec2 mst = gl_FragCoord.xy/u_mouse.xy;
    float mdist= distance(vec2(1.0,1.0), mst);
    float dist = distance(st,vec2(sin(u_time/10.0),cos(u_time/10.0)));
  
    st = tile(st,10.0);
    st = rotate2D(st,dist/(mdist/5.0)*PI*2.0);

    gl_FragColor = vec4(vec3(concentricCircles(st, vec2(0.0,0.0), 5.0, 5.0),concentricCircles(st, vec2(0.0,0.0), 10.0, 10.0),concentricCircles(st, vec2(0.0,0.0), 20.0, 10.0)),1.0);
}
```
#### .vert file
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
