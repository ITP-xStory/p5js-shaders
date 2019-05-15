# How to write a shader

Now let's talk about the shader files we're loading.

To make a shader you must make two files in your p5 sketch folder: A **"shader.vert"** file and a **"shader.frag"** file. You can name the shader files whatever you want. "myAwesomeShader.vert" and "myAwesomeShader.frag" are completely valid names. As long as you have both the .vert and the .frag file, you are good to go. 

It's important to note that these files are written in GLSL (OpenGL Shading Language), which is a lower level language than Javascript, meaning it speaks more directly to your computer, specifically to your GPU. The code will seem foreign and confusing at first, but with time we can develop more of an understanding of what is going on.

The .vert file handles everything that has to do with vertexes - that is all of your geometry (shapes) and its position on the canvas. The .frag file handles everything that has to do with the actual coloring of the pixels.

The programs run like this:
The .vert file is run first, and automatically passes the calculations we do about the geometry (shapes) on our canvas on to the .frag file. The fragment file then colors the pixels according to their positions!

*The important thing you need to remember now is that the content of these two files will apply to all pixels! These two programs are run simultaneously for every single pixel on your canvas. As we [explained before](https://itp-xstory.github.io/p5js-shaders/#/./docs/what-are-shaders).*



## Content of a shader.vert file

First we write some necessary definitions that let your GPU know how to render the shader. You should not worry too much about this when just starting out with shaders. Just include it. 

[GL_ES](https://www.khronos.org/opengles/) is a shader API which is automatically used by your GPU, if you are displaying the shader in a browser or on a mobile platform. #ifdef means "if defined". It simply makes a global setting for how precise we want our graphics rendered depending on where we are viewing them. So if we are in a browser, they will get the level of precision we define here. In this case we are setting all floating point numbers in our code to have "medium" precision. This is really important for making smooth color gradients,

**"Float types are vital in shaders, so the level of precision is crucial. Lower precision means faster rendering, but at the cost of quality. You can be picky and specify the precision of each variable that uses floating point. In the first line (precision mediump float;) we are setting all floats to medium precision. But we can choose to set them to low (precision lowp float;) or high (precision highp float;)."** - [The book of shaders](https://thebookofshaders.com/02/)


```glsl
#ifdef GL_ES

precision mediump float;

#endif
```



Then we make something called **attributes**, these contain information that is automatically sent to the shader by the p5 sketch.

For shaders in p5 we must make sure one thing is always done in the .vert file: The pixel must be told where on the canvas it belongs! This attribute is called **vec3 aPosition**. You cannot change its name, and the attribute is read-only, meaning you cannot overwrite it. Attributes are usually named with in "a" as a prefix: "aSomething".

The attribute contains position information, it is a vec3 (vector 3), meaning it contains x, y, and z values.


```glsl
attribute vec3 aPosition;

```



All shaders must have a void main() function. This is where the program starts. Remember that everything in here is run for each pixel on the canvas! So you need to adjust your thinking, and think about coding for just one pixel at a time!

For shaders in p5, there is something weird that needs to happen in the main() function.
We need to scale the attribute aPosition before it can be passed to the .frag file. This might be a bug that is resolved in a later version. But for now we can get around it by simply scaling all pixels positions.


First we copy the position data into a vec4 (vector 4), meaning we will now have the following numbers in there (x,y,z,w). We will put in 1.0 as the w parameter (when w = 1.0 the vector is treated as a position, when w = 0.0 the vector is treated as a direction, this is standard vector math). 

Then we scale the pixel position by two, and move it to the center of the screen. If we don't do this, it will appear with its bottom left corner in the center of the sketch. These numbers might not make any sense right now, but they will after you read the [Important shader concepts](https://itp-xstory.github.io/p5js-shaders/#/./docs/important-concepts).

```glsl
positionVec4.xy = positionVec4.xy * 2.0 - 1.0;

```

*This position calculation always needs to be there, whether we choose to use it in the .frag file or not.*


Finally, the vertex shader requires there to be a vec4 output called **gl_Position**. 
This automatically sends the position information on to the fragment shader file. It is good form to put this as the final line of your .vert file.
So let's set it equal to the scaled calculation we made: gl_Position = positionVec4.



```glsl

void main() {

  vec4 positionVec4 = vec4(aPosition, 1.0); // Copy the position data into a vec4, adding 1.0 as the w parameter

  positionVec4.xy = positionVec4.xy * 2.0 - 1.0; // Scale to make the output fit the canvas. 

  gl_Position = positionVec4;

}

```



## Content of shader.frag file

Before we get into how to use the position information from the .vert file, let's disregard it for a moment, and instead just color everything one color. 


In our .frag file, we have the same definition as before, and we also have a **main()** function that runs the program.

We will make a new vec3 called *color* and put a color in it. This is just a variable, so you could it whatever you want. Let's make a blue color. In shaders, the RGB color goes from 0 - 1 instead of 0 - 255. So we do:

```glsl
vec3 color = vec3(0.0, 0.0, 1.0);
```

Finally, the fragment shader requires there to be a vec4 output called **gl_FragColor**. This line is what tells the GPU how to color the pixel. This must be the final line of your .frag file, any code placed after this line will not have any effect, and you might get a "code not reached" error.

**gl_FragColor** expects the format vec4 (r,g,b,a), so we put in 1.0 as our alpha, meaning no transparency.


```glsl

gl_FragColor = vec4(color, 1.0);
```


The whole code looks like this:


```glsl

#ifdef GL_ES

precision mediump float;

#endif


void main() {

    // Make a blue color. In shaders, the RGB color goes from 0 - 1 instead of 0 - 255

    vec3 color = vec3(0.0, 0.0, 1.0);

    gl_FragColor = vec4(color, 1.0);

}

```


___________________________________________

# Shader code: One color fill



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

    // Make a blue color. In shaders, the RGB color goes from 0 - 1 instead of 0 - 255
    vec3 color = vec3(0.0, 0.0, 1.0);

    gl_FragColor = vec4(color, 1.0);

}

```
