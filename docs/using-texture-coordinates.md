# Using Texture Coordinates
TexCoord and FragCoord — you may have noticed these variables in our shader examples code but not understood exactly what they meant. Think about your shader as a stretchy cloth and these coord variables as a series of pins that hold the corners tight. Depending on the location and spacing of these pins your shader can be stretched, distorted, or duplicated in different ways.

TexCoord is calculated in the vertex and passed from the vertex to the fragment shader, and then the texture coordinates of our shader are set to be equal to TexCoord.

The .vert file, but only the code related to TexCoord:
```vert
// our texcoordinates from the cpu
attribute vec2 aTexCoord;

// this is a variable that will be shared with the fragment shader
// we will assign the attribute texcoords to the varying texcoords to move them from the vert shader to the frag shader
// it can be called whatever you want but often people prefix it with 'v' to indicate that it is a varying
varying vec2 vTexCoord;

void main() {

  // copy the texture coordinates
  vTexCoord = aTexCoord;
  
}
```
The .frag file, but only the code related to TexCoord:
```frag
   // recieve vTexCoord from vertex shader
   varying vec2 vTexCoord;
  
  // vTexCoord is a value that goes from 0.0 - 1.0 depending on the pixels location
  // we can use it to access every pixel on the screen
  vec2 st = vTexCoord;
```

FragCoord is used within the fragment shader to calculate the texture coordinates relative to the resolution uniform we passed it.<br><br>
The .frag file, but only the code related to FragCoord:
```frag
void main (void) {
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
 }
```

You may see coordinates referred as uv, st, coord, etc. these are just different naming conventions for the same thing.