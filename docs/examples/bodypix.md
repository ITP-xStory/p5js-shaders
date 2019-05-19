# Bodypix Feedback Loop
#### This example use bodypix to remove the background from the webcam video, and passes it to the shader as a texture, which creates a feedback loop from the video frames.

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/shader-with-webcam?path=webcam.frag&previewSize=100"
    alt="shader-with-webcam on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

***

#### .js file
```javascript
/* See the blog post about BodyPix here: https://medium.com/tensorflow/introducing-bodypix-real-time-person-segmentation-in-the-browser-with-tensorflow-js-f1948126c2a0 */
/* This is a modified version of a bodypix example by Dan Oved https://twitter.com/oveddan */

let statusText = '';
// the downloaded bodyPix machine learning model
let model;
// the video capture
let capture;
// the most recent resulting mask image generated from estimating person segmentation on the video 
let maskImage;
// the output canvas
let canvas;

let maskBackgroundButton;

let camShader;
let shaderLayer;
let copyLayer

let w, h;

function preload(){
  // load the shader
  camShader = loadShader('uniform.vert', 'uniform.frag');
}

function setup() {
  
  w = 1280;
  h = 1024;
  // save the created canvas so it can be drawn on with bodypix later.
  // createCanvas(windowWidth, windowHeight);
  canvas = createCanvas(w, h).canvas;
 
  loadModelAndStartEstimating();
  
  // capture from the webcam
  capture = createCapture(VIDEO);
  capture.hide();
  
  shaderLayer = createGraphics(w, h, WEBGL);
  shaderLayer.noStroke();
  
  copyLayer = createGraphics(w, h);
  copyLayer.noStroke();
  
}

/* the arguments to the function which draws the mask onto the canvas.  See the documentation for full descriptions:
https://github.com/tensorflow/tfjs-models/tree/master/body-pix#drawmask
*/
// opacity of the segmentation mask to be drawn on top of the image.
const maskOpacity = 1.0;
// if the output should be flip horizontally.  This should be set to true for user facing cameras.
const flipHorizontal = true;
// how much to blur the mask background by.  This affects the softness of the edge.
const maskBlurAmount = 3;

function draw() {
  
 
  
  
  background(255);
  
  textSize(16);
  text(statusText, 0, 30);
  
  // make sure video is loaded, and a mask has been estimated from the video.  The mask
  // continuously gets updated in the loop estimateFrame below, which is independent
  // from the draw loop
  if (capture && capture.loadedmetadata && maskImage) {
    const videoFrame = capture.get(0, 0, capture.width, capture.height);
    
    // use bodyPix to draw the estimated video with the most recent mask on top of it onto the canvas.
    bodyPix.drawMask(canvas, videoFrame.canvas, maskImage, maskOpacity, maskBlurAmount, flipHorizontal);
  }
  
  let c = get();
  
  shaderLayer.shader(camShader);
  
  camShader.setUniform('tex0', c);
  camShader.setUniform('tex1', copyLayer);
  camShader.setUniform('mouseDown', int(mouseIsPressed));
  camShader.setUniform('time', frameCount * 0.01);
  
  shaderLayer.rect(0,0,w,h);
  
  copyLayer.image(shaderLayer, 0,0,w,h);
  
  image(shaderLayer,0,0,w,h);
  
}

async function loadModelAndStartEstimating() {
  setStatusText('loading the model...');
  model = await bodyPix.load();
  
  setStatusText('');
  
  // start the estimation loop, separately from the drawing loop.  
  // This allows drawing to happen at a high number of frame per
  // second, independent from the speed of estimation.
  startEstimationLoop();
}

function startEstimationLoop() {  
  estimateFrame();
}

async function estimateFrame() {
  if (capture &&  capture.loadedmetadata) {
    await performEstimation();
  }
  
  // at the end of estimating, start again after the current frame is complete.
  requestAnimationFrame(estimateFrame);
}


/* the arguments to the functions which estimate the person segmentation and convert the results
to a mask to draw onto the canvas.  See the documentation for both methods:
https://github.com/tensorflow/tfjs-models/tree/master/body-pix#person-segmentation
https://github.com/tensorflow/tfjs-models/tree/master/body-pix#tomaskimagedata
*/
// set the output stride to 16 or 32 for faster performance but lower accuracy.
const outputStride = 8;
// affects the crop size around the person.  Higher number is tighter crop and visa
// versa for a lower number
const segmentationThreshold = 0.5;
// if the background or the person should be masked.  If set to false, masks the person.
const maskBackground = true;

async function performEstimation() {
  const videoFrame = capture.get(0, 0, capture.width, capture.height);
  const personSegmentation = await model.estimatePersonSegmentation(
    videoFrame.canvas, outputStride, segmentationThreshold);
  
    maskImage = bodyPix.toMaskImageData(personSegmentation, maskBackground, flipHorizontal);
  
 
}

function setStatusText(text) {
  statusText = text;
}
```

#### .frag file
```frag
precision mediump float;

#define PI 3.14159265359
#define TWO_PI 6.28318530718

// grab texcoords from vert shader
varying vec2 vTexCoord;

// our textures coming from p5
uniform sampler2D tex0;
uniform sampler2D tex1;

uniform float mouseDown;
uniform float time;

vec3 rgb2hsb(vec3 c){
    vec4 K = vec4(0.0, -1.0 / 3.0, 2.0 / 3.0, -1.0);
    vec4 p = c.g < c.b ? vec4(c.bg, K.wz) : vec4(c.gb, K.xy);
    vec4 q = c.r < p.x ? vec4(p.xyw, c.r) : vec4(c.r, p.yzx);

    float d = q.x - min(q.w, q.y);
    float e = 1.0e-10;
    return vec3(abs(q.z + (q.w - q.y) / (6.0 * d + e)), d / (q.x + e), q.x);
}

vec3 hsb2rgb(vec3 c){
    vec4 K = vec4(1.0, 2.0 / 3.0, 1.0 / 3.0, 3.0);
    vec3 p = abs(fract(c.xxx + K.xyz) * 6.0 - K.www);
    return c.z * mix(K.xxx, clamp(p - K.xxx, 0.0, 1.0), c.y);
}

void main() {

  vec2 uv = vTexCoord;
  // the texture is loaded upside down and backwards by default so lets flip it
  uv.y = 1.0 - uv.y;
  

  // make a copy of the uvs
  vec2 feedbackUv = uv;

  // move the uv space between -1 and 1
  feedbackUv = uv * 2.0 - 1.0;

  // scale the uvs up just a tad for a feedback zoom
  feedbackUv *= 0.98;

  // return the uvs to 0 - 1 range
  feedbackUv = feedbackUv * 0.5 + 0.5;
  
  // get the webcam
  vec4 cam = texture2D(tex0, uv);

  // make a copy of the camera
  vec4 tex = cam;
  
  // if the mouse isn't clicked we'll run the feedback loop
  if(mouseDown == 0.0){

    // calculate an angle from the hue
    // we will use these to offset the texture coordinates just a little bit
    vec3 hsb = rgb2hsb(cam.rgb);
    float angleX =  cos(hsb.r*TWO_PI);
    float angleY = sin(hsb.r * TWO_PI);

    // add those angles to the tex coords and sample the feed back texture
    tex = texture2D(tex1, feedbackUv + vec2(angleX, angleY)*0.001);

    // add some camera from the screen
    tex.rgb += cam.rgb * 0.9;

    // // if tex.r > 1.0, invert the texture and swizzle the color channels around
    // tex.r = mix(tex.r, 1.0 / tex.r, step(0.9, tex.r) );
    // tex.g = mix(tex.r, 1.0 / tex.g, step(0.9, tex.g) );
    // tex.b = mix(tex.r, 1.0 / tex.b, step(0.9, tex.b) );
    tex.rgb = mix(tex.rgb, 1.0 - tex.rgb, step(hsb.b, (tex.r+tex.g+tex.b)/3.0) );
    
    // tex.rgb = tex.rgb / hsb.rgb;
    tex.rgb = (hsb.g / tex.rgb) + 1.0;
    // tex.rgb = 1.0 - tex.rgb;
    // tex /= 2.0;

  } 
  
  // render the output
  gl_FragColor = tex;
}
```


#### .vert file
```vert
// vert file and comments from adam ferriss
// https://github.com/aferriss/p5jsShaderExamples

// our vertex data
attribute vec3 aPosition;
attribute vec2 aTexCoord;

// lets get texcoords just for fun! 
varying vec2 vTexCoord;

void main() {
  // copy the texcoords
  vTexCoord = aTexCoord;

  // copy the position data into a vec4, using 1.0 as the w component
  vec4 positionVec4 = vec4(aPosition, 1.0);
  positionVec4.xy = positionVec4.xy * 2.0 - 1.0;

  // send the vertex information on to the fragment shader
  gl_Position = positionVec4;
}
```
