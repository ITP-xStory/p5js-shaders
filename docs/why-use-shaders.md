# Why use shaders?

## Performance

If you've ever used loadPixels(), set(), and updatePixel() and done even simple pixel-by-pixel math in p5.js then you're aware of the limitations of working in that way.

Below is an example comparing the same operation (distance from mouse) to change pixels values, first using loadPixels in p5 and then using a shader loaded in p5. Click the buttons in the top left of the sketch to see the difference in performance.

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/shader-performance?path=sketch.js&previewSize=100"
    alt="shader-performance-1 on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>

This huge difference in performance (7 fps vs 150 fps depending on the machine) is due to p5 performing this operation in sequence versus the shader performing it in parallel, more on this in ["what are shaders?"](https://itp-xstory.github.io/p5js-shaders/#/./docs/what-are-shaders).

--------------------->>>>>>>>>>


### Visual Complexity

....but you will see how a shader works and how fast it runs even though we are putting some pretty complex graphics in your sketch.

EXAMPLE THAT IS REALLY COMPLEX! LIKE A MANDELBROT OR SOMETHING. \
Just to show power of shaders.



