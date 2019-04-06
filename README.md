Glitch.com examples: [https://glitch.com/@kcconch/shader-collection](https://glitch.com/@kcconch/shader-collection)

# Getting started with shaders using p5.js

This will live as a wiki page on p5js github: [https://github.com/processing/p5.js/wiki](https://github.com/processing/p5.js/wiki) 


# 


# Overview / to write

_The following is an overview of the Reference/tutorial we are writing for p5.js including a list of the examples that must be made. \
We are trying to bridge the gap for a programmer who just started programming (p5 community) to understand how to make shaders._

#

**Using shaders with p5**



*   A soft introduction to shaders, getting started
*   What are they
*   How to use a shader
    *   The space between 0 and 1
    *   Making variables and changing them
*   The power of shaders
*   When to use them with p5
*   Good resources for learning more about shaders on the web
    *   The book of shaders
    *   … etc.
    *   Link to example on how to load shaders from these sources
    *   A list of common words found in shader literature
        *   BoS Related: pct, st
        *   Swizzling
        *   Uniforms
        *   Shaping Functions
        *   Rendering
        *   Pixel
        *   Texel

**Using shaders with p5 (examples)**



*   Loading a shader onto a canvas
*   Loading several shaders onto a canvas / several canvas?
*   Loading a shader onto a 3D object / 3D primitive

--------------------------------------------------------------------------------------------

**Interactivity with shaders**



*   Reminder: Uniforms
*   Etc.

**Interactivity with shaders (examples)**



*   Mouse x,y
*   Keys for events (draw a shape / trigger a wave)
*   Sliders

--------------------------------------------------------------------------------------------

**Using the camera in your shaders**



*   Adam Ferris examples...

--------------------------------------------------------------------------------------------

**Setting up shaders from external sources**



*   load shaders from ___ in P5.
    *   Book of Shaders
    *   ShaderToy javascript files

--------------------------------------------------------------------------------------------

**More shader examples (resource list)**



*   Adam Ferris has many great examples


# 


[TOC]



# 


# The Reference


## Getting started with shaders using p5

Shaders are a great way to make graphics for your projects. So far you have probably made graphic shapes like **rect**() or **ellipse**() or maybe even 3D shapes like a **plane**() or a **box**(), and you should be more than familiar with using **fill**() to decide the color of these objects. But wouldn't it be great if we could make these objects have moving graphics on them? And without slowing down our sketch?

Enter the shader!

(IMG different examples of shaders on stuff in p5)


## What is a shader?

First of all, even though you may never have written a shader before you have definitely come across them! Every 3D object in every computer game ever has a shader attached to the it, the shader decides what color the object takes, but also how light bounces off the object, whether it is transparent and more. Also, if you have ever been to a concert where there were visuals which reacted to the sound or movements, these were likely shaders! 

A shader is a small program that runs entirely on your graphics card, the GPU (Graphics Processing Unit), rather than the CPU (Central Processing Unit) of your computer. This makes it incredible fast.

While your CPU works by doing every calculation - every move of a pixel or shape you have been drawing until now - in sequence, meaning it takes each one and thinks about it, one a at time - your GPU is actually capable of something called parallel processing.

This means that everything you draw in your sketch by using a shader is drawn all at once, using the GPU! Therefor shaders are an incredible optimized way of making more complex drawings, without slowing your sketch down.

To understand what a shader does, imagine the CPU as a single pipe, where all of the pixels on your canvas must pass through, one at a time, to be told what color they should have. Now imagine instead, that each pixel has its own pipe, and we could just tell every pixels on your canvas what color it should have in parallel (at the same time). That is what a shader does. 

It is kind of like painting the Mona Lisa with a blast canon.

[https://www.youtube.com/watch?v=-P28LKWTzrI](https://www.youtube.com/watch?v=-P28LKWTzrI) 
