# Porting Shadertoy Code to p5.js Shaders

Shadertoy is an excellent resource for getting inspiration for your shader code.
Browsing through the examples you may see a noise function, for example, that would be useful in a sketch you're working on.
Shadertoy has some real master shader alchemists and we can become their apprentices by sifting through their formulas and equations,
just make sure however to give proper attribution and pay attention to any licensing specified by the author.

***

### u_uniform vs iUniform
In shadertoy code your vert code is handled for you, as well as having many included shader inputs (uniforms). These uniforms are identified by a "i" prefix, ex: iMouse. A big part of using converting shadertoy code to p5.js is figuring out and sending each uniform the data that it needs.

# Example - [Recursive Noise Experiment by ompuco](https://www.shadertoy.com/view/wllGzr)
## Shadertoy Code
```glsl
float hash( float n )
			{
			    return fract(sin(n)*43758.5453);
			}

			float noise( vec3 x )
			{
			    // The noise function returns a value in the range -1.0f -> 1.0f

			    vec3 p = floor(x);
			    vec3 f = fract(x);

			    f       = f*f*(3.0-2.0*f);
			    float n = p.x + p.y*57.0 + 113.0*p.z;

			    return mix(mix(mix( hash(n+0.0), hash(n+1.0),f.x),
			                   mix( hash(n+57.0), hash(n+58.0),f.x),f.y),
			               mix(mix( hash(n+113.0), hash(n+114.0),f.x),
			                   mix( hash(n+170.0), hash(n+171.0),f.x),f.y),f.z)-.5;
			}


void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    
    
    vec3 t = (float(iFrame)*vec3(1.0,2.0,3.0)/1.0)/1000.0;//+iMouse.xyz/1000.0;

    
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    uv=uv/4.0+.5;
    uv-=iMouse.xy/iResolution.xy/4.0;

    vec3 col = vec3(0.0);
    
    
    
    for(int i = 0; i < 16; i++){
        float i2 = float(i)*1.0;
					col.r+=noise(uv.xyy*(12.0+i2)+col.rgb+t*sign(sin(i2/3.0)));
					col.g+=noise(uv.xyx*(12.0+i2)+col.rgb+t*sign(sin(i2/3.0)));
					col.b+=noise(uv.yyx*(12.0+i2)+col.rgb+t*sign(sin(i2/3.0)));
				}
				

				for(int i = 0; i < 16; i++){
                    float i2 = float(i)*1.0;
					col.r+=noise(uv.xyy*(32.0)+col.rgb+t*sign(sin(i2/3.0)));
					col.g+=noise(uv.xyx*(32.0)+col.rgb+t*sign(sin(i2/3.0)));
					col.b+=noise(uv.yyx*(32.0)+col.rgb+t*sign(sin(i2/3.0)));
				}
				col.rgb/=32.0;
				col.rgb=mix(col.rgb,normalize(col.rgb)*2.0,1.0);
				col.rgb+=.3;
    
    

    // Output to screen
    fragColor = vec4(col,1.0);
}
```
