1. Uniform
2. Attributes
3. Other common variables

# Making variables and changing them

By now you will have noticed that we are using vectors a lot in shaders. We use them to enter in our numbers for different variables. So for now just get used to writing your numbers this way.

We need to tell the variable how many numbers they will contain, so we use types: float, vec2, vec3, vec4, vec5 etc. The number is the amount of parameters you intend to use. 

```glsl

float scalar = 5.2; // for instance: a scalar that could manipulate the color in some way

vec2 position = vec2(0.5, 0.5);   // for instance: the center of the canvas (width/2, height/2)

vec3 color = vec3(0.0, 0.5, 1.0); // for instance: R = 0, G = 0.5, B = 1.0

vec4 colorWithAlpha = vec4(0.0, 0.5, 1.0, 0.5); // for instance: color that is half transparent A = 0.5

```

The neat way about constructing our variables this way is the we can access their components easily.

We can access these as color.r, color.g, color.b

If we write color.rgb we get all three!

(We could also do color.xyz, it'd be the same.)

This is called Swizzling.
