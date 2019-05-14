# What is a shader?

First of all, even though you may never have written a shader before, you have definitely come across them! For instance every 3D object in every computer game ever made, has a shader attached to the it. The shader decides what color the object takes, but also how light bounces off the object, whether it is transparent and much more. Also, if you have ever been to a concert where there were visuals which reacted to the sound or movements, these were likely shaders! 

A shader is a small program that runs entirely on your graphics card, the GPU (Graphics Processing Unit), rather than the CPU (Central Processing Unit) of your computer. This makes it incredible fast. They are actually easy to write, and simple to implement in p5, once you understand the basics of how they work.

Since a shader is it's own little script you place it in a seperat file from your p5 sketch.js, but we will get back to that.


# How does a shader work?

What you have been doing until now relies mostly on the CPU. The CPU works by doing every calculation - every move of a pixel or shape you have been drawing until now - in sequence. If you look really closely at you screen you will see every tiny pixel it is made up of. When you are drawing using the CPU, the computer has to make each operation you tell it to do (for instance draw the color red, or increase the brightness), one pixel at a time. Think about it, one pixel at a time! If your screen is 2,560×1,600 pixels, that's 4.000.096 operations per frame. At around 30 frames or more per second that's 122.880.000 operations per second. 

In contrast your GPU (Graphics Processing Unit) is capable of something called parallel processing by using shaders. This means that everything you draw in your sketch by using a shader is drawn all at once, all 4.000.096 operations! Therefor shaders are an incredible optimized way of making more complex drawings, without slowing your sketch down.

To understand what a shader does, imagine the CPU as a single pipe, where all of the pixels on your canvas must pass through, one at a time, to be told what color they should have. Now imagine instead, that each pixel has its own pipe, and we could just tell every pixels on your canvas what color it should have in parallel (at the same time). That is what a shader does. 

It is kind of like painting the Mona Lisa with a blast canon.

[https://www.youtube.com/watch?v=-P28LKWTzrI](https://www.youtube.com/watch?v=-P28LKWTzrI) 