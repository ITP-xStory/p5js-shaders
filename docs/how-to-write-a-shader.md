# How to write a shader

Now let's talk about the shader files we're loading.

To make a shader you must make two files in your p5 sketch folder: A **"shader.vert"** file and a **"shader.frag"** file. You can name the shader files whatever you want. "myAwesomeShader.vert" and "myAwesomeShader.frag" are completely valid names. As long as you have both the .vert and the .frag file, you are good to go. 

It's important to note that these files are written in GLSL (OpenGL Shading Language), which is a lower level language than Javascript, meaning it speaks more directly to your computer, specifically to your GPU. The code will seem foreign and confusing at first, but with time we can develop more of an understanding of what is going on.

The .vert file handles everything that has to do with vertexes - that is all of your geometry (shapes) and its position on the canvas. The .frag file handles everything that has to do with the actual coloring of the pixels.

The programs run like this:
The .vert file is run first, and automatically passes the calculations we do about the geometry (shapes) on our canvas on to the .frag file. The fragment file then colors the pixels according to their positions!

*The important thing you need to remember now is that the content of these two files will apply to all pixels! These two programs are run simultaneously for every single pixel on your canvas. As we [explained before](https://itp-xstory.github.io/p5js-shaders/#/./docs/what-are-shaders).*

Let us look at a simple example, where we make our own one-color fill.

___________________________________________

## Content of a shader.vert file

First we write some necessary definitions that let your GPU know how to render the shader. You should not worry too much about this when just starting out with shaders. Just include it. 

[GL_ES](https://www.khronos.org/opengles/) is a shader API which is automatically used by your GPU, if you are displaying the shader in a browser or on a mobile platform. #ifdef means "if defined". It simply makes a global setting for how precise we want our graphics rendered depending on where we are viewing them. So if we are in a browser, they will get the level of precision we define here. In this case we are setting all floating point numbers in our code to have "medium" precision. This is really important for making smooth color gradients,

***"Float types are vital in shaders, so the level of precision is crucial. Lower precision means faster rendering, but at the cost of quality. You can be picky and specify the precision of each variable that uses floating point. In the first line (precision mediump float;) we are setting all floats to medium precision. But we can choose to set them to low (precision lowp float;) or high (precision highp float;)." [The book of shaders](https://thebookofshaders.com/02/)***


```glsl

#ifdef GL_ES

precision mediump float;

#endif
```


Then we make something called **attributes**, these contain information that is automatically sent to the shader by the p5 sketch.

For shaders in p5 we must make sure one thing is always done in the .vert file: The pixel must be told where on the canvas it belongs! This attribute is called **vec3 aPosition**. You cannot change its name, and the attribute is read-only, meaning you cannot overwrite it later the .vert file. Attributes are usually named with in "a" as a prefix: "aSomething".

The attribute contains position information, it is a vec3 (vector 3), meaning it contains x, y, and z values.


```glsl

attribute vec3 aPosition;

```


All shaders must have a void main() function. This is where the program starts. Remember that everything in here is run for each pixel on the canvas! So you need to adjust your thinking to think about coding for just one pixel at a time!

For shaders in p5 there is something weird that needs to happen in the main() function. We need to scale the attribute aPosition. This might be a bug that is resolved in a later version. But for now we can get around it by simply scaling all pixels positions. 

First we copy the position data into a vec4 (vector 4), meaning we will now have the following numbers in there (x,y,z,w). We will put in 1.0 as the w parameter (when w = 1.0 the vector is treated as a position, when w = 0.0 the vector is treated as a direction, this is standard vector math ---------------------->>>> LINK to mathisfun). 

Then we scale the pixel position by two, and move it to the center of the screen (by doing -1.0). If we don't do this, it will appear with its bottom left corner in the center of the sketch. 

Finally, the vertex shader requires there to be a vec4 output called **gl_Position**. This sends the position information on to the fragment shader file. This is done automatically, as long as you put it into the built in shader function "gl_Position".


```glsl

void main() {

  vec4 positionVec4 = vec4(aPosition, 1.0); // Copy the position data into a vec4, adding 1.0 as the w parameter

  positionVec4.xy = positionVec4.xy * 2.0 - 1.0; // Scale to make the output fit the canvas. 

  gl_Position = positionVec4;

}

```



### Content of shader.frag file

```glsl

// These are necessary definitions that let you graphics card know how to render the shader

#ifdef GL_ES

precision mediump float;

#endif

// We are not going to do anything with the position data in this example, because we are coloring everything blue, so we are actually not using anything from our .vert program. However the position calculation still needs to be there.

void main() {

    // Make a blue color

    // In shaders, the RGB color goes from 0 - 1 instead of 0 - 255

    vec3 color = vec3(0.0, 0.0, 1.0);

    // gl_FragColor is a built in shader variable, and you .frag file must contain it

    // We are setting the vec3 color into a new vec4, with an transparency of 1 (no opacity)

    gl_FragColor = vec4(color, 1.0);

}

```


## Shader code

**Coloring the background using a shader / making a fill**

[https://glitch.com/~one-color](https://glitch.com/~one-color) 


### Content of shader.vert file

```glsl

#ifdef GL_ES

precision mediump float;

#endif



attribute vec3 aPosition;

attribute vec3 aPosition;



void main() {

  vec4 positionVec4 = vec4(aPosition, 1.0);

  // scale the rect by two, and move it to the center of the screen - if we don't do this, it will appear with its bottom left corner in the center of the sketch

  positionVec4.xy = positionVec4.xy * 2.0 - 1.0;

  gl_Position = positionVec4;

}

```

### Content of shader.frag file

```glsl

#ifdef GL_ES

precision mediump float;

#endif



void main() {

    vec3 color = vec3(0.0, 0.0, 1.0);

    gl_FragColor = vec4(color, 1.0);

}

```
