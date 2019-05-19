# Applying Shaders as Textures
#### This is an example showing how to apply your shaders to 2D/3D shapes in p5.

We use the p5 [**texture()**](https://p5js.org/reference/#/p5/texture). This works like fill() and will put the shader on any shape you draw after the **texture(shaderTexture)** call.

Try remixing and replacing the shader files with your own shader files.

Sidenote: When you put a texture or shader on an ellipse it needs to be rendered in 3D, so a fifth parameter that controls the number of vertices in it becomes necessary, or else you'll have sharp corners. Setting it to 100 is smooth. mouseX is being mapped to control this number.

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/shader-as-texture?path=texture.frag&previewSize=100"
    alt="shader-as-texture on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

***

#### .js file
```javascript
let theShader;
let shaderTexture;

let theta = 0;

let x;
let y;
let outsideRadius = 200;
let insideRadius = 100;


function preload(){
  // load the shader
  theShader = loadShader('texture.vert','texture.frag');
}

function setup() {
  // shaders require WEBGL mode to work
  createCanvas(710, 400, WEBGL);
  noStroke();

  // initialize the createGraphics layers
  shaderTexture = createGraphics(710, 400, WEBGL);

  // turn off the createGraphics layers stroke
  shaderTexture.noStroke();

   x = -50;
   y = 0;
}

function draw() {

  // instead of just setting the active shader we are passing it to the createGraphics layer
  shaderTexture.shader(theShader);

  // here we're using setUniform() to send our uniform values to the shader
  theShader.setUniform("resolution", [width, height]);
  theShader.setUniform("time", millis() / 1000.0);
  theShader.setUniform("mouse", [mouseX, map(mouseY, 0, height, height, 0)]);

  // passing the shaderTexture layer geometry to render on
  shaderTexture.rect(0,0,width,height);

  background(255);
  
  // pass the shader as a texture
  // anything drawn after this will have this texture.
  texture(shaderTexture);
  
  translate(-150, 0, 0);
  push();
  rotateZ(theta * mouseX * 0.0001);
  rotateX(theta * mouseX * 0.0001);
  rotateY(theta * mouseX * 0.0001);  
  theta += 0.05;
  sphere(125);
  pop();
  
  /* when you put a texture or shader on an ellipse it is rendered in 3d,
     so a fifth parameter that controls the # vertices in it becomes necessary,
     or else you'll have sharp corners. setting it to 100 is smooth. */
  let ellipseFidelity = int(map(mouseX, 0, width, 8, 100));
  ellipse(260, 0, 200, 200, ellipseFidelity);
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

#### .frag file
```frag
precision mediump float;
#endif

#define PI 3.14159265358979323846

uniform vec2 resolution;
uniform float time;
uniform vec2 mouse;

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

float concentricCircles(in vec2 st, in vec2 radius, in float res, in float scale) {
    float dist = distance(st,radius);
    float pct = floor(dist*res)/scale;
    return pct;
}

void main (void) {
    vec2 st = gl_FragCoord.xy/resolution.xy;
    vec2 mst = gl_FragCoord.xy/mouse.xy;
    float mdist= distance(vec2(1.0,1.0), mst);

    float dist = distance(st,vec2(sin(time/10.0),cos(time/10.0)));
    st = tile(st,10.0);
  
    st = rotate2D(st,dist/(mdist/5.0)*PI*2.0);

    gl_FragColor = vec4(vec3(concentricCircles(st, vec2(0.0,0.0), 5.0, 5.0),concentricCircles(st, vec2(0.0,0.0), 10.0, 10.0),concentricCircles(st, vec2(0.0,0.0), 20.0, 10.0)),1.0);
}
```
