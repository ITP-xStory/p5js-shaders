# What are shaders?

Shaders are used all around us, so you have definitely seen them before, even though you have never written one yourself.
Shaders are necessary for fast real-time rendering of graphics, from computer games to concert visuals.

If you play computer games, then every 3D object in it has a shader attached to the it's material. The shader decides what color the object takes, but also how light bounces off the object, whether it is transparent and much more. Also, if you have ever been to a concert where visuals were reacting to the sound or movements, these were likely shaders!



A shader is a small program that runs entirely on your graphics card, the GPU (Graphics Processing Unit), rather than the CPU (Central Processing Unit) of your computer. This makes it incredible fast. They are actually easy to write, and simple to implement in p5, once you understand the basics of how they work.

Since a shader is it's own script you simply place it in a seperate file from your p5 sketch.js, just like when you load in a soundfile or image, but we will get to that in a moment. First it is important to understand why shaders are different from the programming you normally do when you write your p5 sketch. 


### How does a shader work?

What you have been doing until now when you are drawing shapes in [**function draw()**](https://p5js.org/reference/#/p5/draw) in p5, relies mostly on the CPU.

The CPU works by doing every calculation - every move of a pixel or shape you have been drawing until now - in sequence. If you look really closely at your screen you will see every tiny pixel it is made up of. When you are drawing using the CPU, the computer has to make each operation you tell it to do (for instance draw the color red, or increase the brightness), one pixel at a time. Think about it, one pixel at a time! If your screen is 2,560×1,600 pixels, that's 4,096,000 operations per frame. At 60 frames per second that's 245,760,000 operations per second. If your screen is 4k, double that number.
This is fine for basic programs, but you might have noticed, that the more complex your drawings become, the slower the whole program runs.

In contrast your GPU (Graphics Processing Unit) is capable of something called parallel processing by using shaders. This means that everything you draw in your sketch by using a shader is drawn all at once, all 4.000.096 operations! Therefor shaders are an incredible optimized way of making more complex drawings, without slowing your sketch down.

To understand what a shader does, imagine the CPU as a single pipe, where all of the pixels on your canvas must pass through, one at a time, to be told what color they should have. Now imagine instead, that each pixel has its own pipe, and we could just tell every pixels on your canvas what color it should have in parallel (at the same time). That is what a shader does. 

It is kind of like painting the entire Mona Lisa in one go with a multicolored blast canon.
We'll let MythBusters demonstrate:

<iframe width="560" height="315" src="https://www.youtube.com/embed/-P28LKWTzrI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

[https://www.youtube.com/watch?v=-P28LKWTzrI](https://www.youtube.com/watch?v=-P28LKWTzrI) 
