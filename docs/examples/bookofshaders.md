# Going through the Book of Shaders with p5

You may have noticed we reference [the Book of Shaders](https://thebookofshaders.com/) a lot and that is for a good reason.
It is a great place to start learning about shaders (that's where we started) and we highly recommend that if we've piqued your interest with our guide that you dive deeper with Patricio.

After you've learned [how to setup up shaders in p5](https://itp-xstory.github.io/p5js-shaders/#/./docs/setting-up-shaders-in-p5) or [how to use p5.js shaders on glitch](https://itp-xstory.github.io/p5js-shaders/#/./docs/setting-up-shaders-in-p5?id=setting-up-p5js-in-glitch) it's as simple passing the same uniforms that are used in the Book of Shaders, and following along with the code in your .frag file.

```javascript
  theShader.setUniform("u_resolution", [width, height]);
  theShader.setUniform("u_time", millis() / 1000.0);
  theShader.setUniform("u_mouse", [mouseX, map(mouseY, 0, height, height, 0)]);
```

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe
    allow="geolocation; microphone; camera; midi; vr; encrypted-media"
    src="https://glitch.com/embed/#!/embed/speckled-petalite?path=shader.frag&previewSize=0"
    alt="speckled-petalite on Glitch"
    style="height: 100%; width: 100%; border: 0;">
  </iframe>
</div>


