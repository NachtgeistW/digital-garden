# My take on shaders: VFX Master Shader (Part I)

By [Harry Alisavakis](https://halisavakis.com/author/tofa/ "Posts by Harry Alisavakis") on [Sunday, June 2, 2019](https://halisavakis.com/2019/06/)
[![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2019/06/screen_1920x1080_2019-06-02_03-37-49.png?fit=780%2C439&ssl=1)](https://halisavakis.com/my-take-on-shaders-vfx-master-shader-part-i/ "Permalink to: "My take on shaders: VFX Master Shader (Part I)"")

As some of you might have noticed, I recently dove into the interesting field of VFX, as it felt like it combined my love for shaders (and technical art in general) with an artistic process which has a lot of impact on the aesthetics of a game as well as its feel and gameplay.

While I experimented with different VFX concepts, I noticed that I was using a bunch of common shader techniques to create new shaders again and again, and I thought to myself: “Why not have one shader to rule them all?” (or “Ash shadérg durbatulûk”). And that’s what I did, and that’s what I’ll be sharing in this three-part tutorial (I’m currently intending for 3 parts at least, don’t know if I’ll do more). The concept for these posts is as follows:

- Part I : First half of opaque VFX Master Shader’s effects (the VFX Apprentice Shader as I’ll be calling it, due to the fact that I think I’m funny).
- Part II: The second half of the opaque VFX Master Shader, so that the circle will be ｃｏｍｐｌｅｔｅ (this is a pun that you won’t get until the second part is out).
- Part III: The transparent VFX Master shader, incorporating all the functionality of the opaque one, plus some more!
- Part ???: Should I enhance the functionality of these shaders, I’ll probably release more parts. So stay tuned, just in case!

Most of the effects discussed in this multi-part tutorial have actually been covered in one way or another, and I’ll link the previous tutorials on the effects as I go. Good thing is that some of the older tutorials are pretty old (not too outdated though) and some of the code can be a bit on the inefficient side. Therefore, these posts also serve as a form of errata for the previous tutorials.

Besides the effects themselves, however, this shader also contains some interesting bits concerning shader variants. See, while I did want a master shader with all the bells and whistles, I wouldn’t want it to have all the features on while I only used a few of them, it wouldn’t be cool for performance and such. For this reason, I’m using shader features (more on that in [Unity’s official manual on shader variants](https://docs.unity3d.com/Manual/SL-MultipleProgramVariants.html)) to turn some features on and off. In this first part I only use it for the vertex offset, but it’s cool intro to that concept. The drawback of this method is that having a lot of these features makes *a lot* of multiple shader variants, which can burden the loading times of your shaders/building time. Specifically, if you have N shader features, you end up with 2ᴺ shader variants, as Unity generates a variant for each possible subgroup of the shader features. So adding just 10 shader features leaves you with 1024 more shader variants. Not ideal. Good thing is that Unity now doesn’t include unused shader variants, but it’s still something I had to mention.

So let’s move on to the code, shall we?

```
?123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105106107108109110111112113114115116117118119120121122123124125126127128129130131Shader "VFX/VFXApprenticeShader"{    Properties    {        _MainTex ("Texture", 2D) = "white" {}        _GradientMap("Gradient map", 2D) = "white" {}          [Space(20)]        [HDR]_Color("Color", Color) = (1,1,1,1)        _PanningSpeed("Panning speed (XY main texture - ZW displacement texture)", Vector) = (0,0,0,0)        _Contrast("Contrast", float) = 1        _Power("Power", float) = 1         //Clipping        [Space(20)]        _Cutoff("Cutoff", Range(0, 1)) = 0        [HDR]_BurnCol("Burn color", Color) = (1,1,1,1)        _BurnSize("Burn size", float) = 0         //Vertex offset        [Space(20)]        [Toggle(VERTEX_OFFSET)]        _VertexOffset("Vertex offset", float) = 0        _VertexOffsetAmount("Vertex offset amount", float) = 0         //Displacement        [Space(20)]        _DisplacementAmount("Displacement", float) = 0        _DisplacementGuide("DisplacementGuide", 2D) = "white" {}    }    SubShader    {        Tags { "RenderType"="Opaque"}        Offset -1, -1        LOD 100         Pass        {            CGPROGRAM            #pragma vertex vert            #pragma fragment frag            #pragma shader_feature VERTEX_OFFSET            // make fog work            #pragma multi_compile_fog             #include "UnityCG.cginc"             struct appdata            {                float4 vertex : POSITION;                float2 uv : TEXCOORD0;                float3 normal : NORMAL;            };             struct v2f            {                float2 uv : TEXCOORD0;                UNITY_FOG_COORDS(1)                float2 displUV : TEXCOORD2;                float4 vertex : SV_POSITION;            };             sampler2D _MainTex;            float4 _MainTex_ST;            sampler2D _GradientMap;            float _Contrast;            float _Power;             fixed4 _Color;             float4 _PanningSpeed;                         float _Cutoff;            fixed4 _BurnCol;            float _BurnSize;             float _VertexOffsetAmount;             sampler2D _DisplacementGuide;            float4 _DisplacementGuide_ST;            float _DisplacementAmount;             v2f vert (appdata v)            {                v2f o;                o.uv = TRANSFORM_TEX(v.uv, _MainTex);                 #ifdef VERTEX_OFFSET                float vertOffset = tex2Dlod(_MainTex, float4(o.uv + _Time.y * _PanningSpeed.xy, 1, 1)).x;                vertOffset = ((vertOffset * 2) - 1) * _VertexOffsetAmount;                v.vertex.xyz += vertOffset * v.normal;                #endif                 o.vertex = UnityObjectToClipPos(v.vertex);                o.displUV = TRANSFORM_TEX(v.uv, _DisplacementGuide);                UNITY_TRANSFER_FOG(o,o.vertex);                return o;            }             fixed4 frag (v2f i) : SV_Target            {                 float2 uv = i.uv;                float2 displUV = i.displUV;                 //UV Panning                uv += _Time.y * _PanningSpeed.xy;                displUV += _Time.y * _PanningSpeed.zw;                 //Displacement                float2 displ = tex2D(_DisplacementGuide, displUV).xy;                displ = ((displ * 2) - 1) * _DisplacementAmount;                 //Contrast and power                float col = pow(saturate(lerp(0.5, tex2D(_MainTex, uv + displ).x, _Contrast)), _Power);                 //Clipping                half test = col - _Cutoff;                clip(test);                 fixed4 rampCol = tex2D(_GradientMap, float2(col, 0));                fixed4 finalCol = fixed4(rampCol.rgb * _Color.rgb * rampCol.a, 1) + _BurnCol * step(test, _BurnSize) * smoothstep(0.001, 0.1, _Cutoff);                                 // apply fog                UNITY_APPLY_FOG(i.fogCoord, finalCol);                return finalCol;            }            ENDCG        }    }}
```

There’s a bunch of stuff here, and while it’s nothing too complicated, the sheer volume is just more than what you’re probably used to so far. So let’s break it down.

## Properties

First of all, I have a property for the main texture that I’ll be using in the shader. Do keep in mind that this shader is designed around using grayscale noise textures, and just works with them (or the red channel of the texture you give as input). Then, I declare another texture which will be a gradient map texture, very much like the one shown in the [gradient mapper post](https://halisavakis.com/my-take-on-shaders-gradient-mapper/). You’ll see what I use it for later (you can probably guess though). It’s crucial that you take a look at that post, because there I have a cool little tool to generate gradient map textures using Unity’s gradient editor, where you can see the changes in real time!

I use the “[Space(20)]” tag to add a bit of space between the properties, just to group them a bit, since there’s quite a few of them in the material inspector. I add a Color property, aptly named “_Color” to which I added the HDR value, to take advantage of bloom post-processing later. I mostly leave it as white, but with a high brightness value, as the color is mostly determined by the gradient map. I also have a vector property named “_PanningSpeed” which adjusts the panning speed for the main texture and the displacement texture (defined later on) in each axis. The “_Contrast” and “_Power” properties are used to adjust the brightness levels of the main texture, to have more control over the rest of the effects. The whole shader basically depends on the brightness value of the main texture.

Later on, there’s the dissolve properties. The “_Cutoff” properties determines the clipping amount, while the “_BurnCol” and “_BurnSize” determine the color and size of the “burning” that occurs around the edges of the dissolved areas. If that rings a bell it’s because I’ve mentioned it in my [dissolve tutorial](https://halisavakis.com/my-take-on-shaders-dissolve-shader/).

Moving on to the vertex offset section, I have a float called “_VertexOffset” and a property determining the amount of vertex offset, called “_VertexOffsetAmount”. Interesting part on shader features is here: the “[Toggle(VERTEX_OFFSET)]” makes the float behave as a toggle and together with a tag that I’ll mention later, it toggles the feature on and off by defining or not the keyword “VERTEX_OFFSET”. More on that later.

Finally, there are the properties for the texture displacement. I add a property for the amount of displacement as well as a property for the texture based on which the displacement will occur. Exactly as shown in the [displacement image effect tutorial](https://halisavakis.com/my-take-on-shaders-waving-displacement/).

## The in-between stuff

Before getting to the vertex and fragment shaders, there are some bits I should point out. In line 34 I use a neat little trick with the “Offset -1, -1” tag. This is to avoid Z-fighting when a face of the object with the shader is in the exact same place as another face. Since I’m using this shader on objects that are exactly at the ground (like aura beams and stuff), it’s nice not to get any Z-fighting.

Also, in line 42 I declare the shader feature “VERTEX_OFFSET” with the tag “#pragma shader_feature VERTEX_OFFSET”, thus signaling that this shader might have more variants depending on whether or not the feature is enabled.

Before the vertex shader I redeclare all the properties from above, with one glowing exception: the “_VertexOffset” property. Since it’s only used for the toggling and just to communicate with ShaderLab, there’s no need to redeclare it in the body of the shader.

I also declare a float4 called “_DisplacementGuide_ST” to use in the vertex shader so that the tiling and offset that we set in the material editor work in our shader for the displacement guide texture. In line 59 I declare a field in the v2f struct called “displUV” that will hold the modified UV coordinates for the texture.

## Vertex shader

Moving on to the vertex shader, things are looking pretty standard. However, in lines 88-92 I take care of all the vertex offsetting that might occur. In line 88 I use the “#ifdef” condition to see if the “VERTEX_OFFSET” toggle has been enabled or not. If it is, the code below is executed as normal, otherwise, the code before the “#endif” on line 92 is like as if it never existed.

Considering it exists, in line 89 I get the main texture’s value to use as a guide for the vertex offset. Do notice that instead of “tex2D” I use “tex2Dlod”, as regular “tex2D” doesn’t really work in the vertex shader. I also add “_Time.y * _PanningSpeed.xy” on the UV’s while sampling the texture, so that it is panning with a speed based on the property we declared.

It’s possible that we have scaled/offset the main texture through the material editor, so it’s important to use “o.uv” instead of “v.uv” after that has been set with the “TRANSFORM_TEX” macro in line 86.

For the vertex offset I treat the black value as negative offset and the white value as positive offset, therefore, in line 90 I map the range of the texture’s value in the [-1,1] range and multiply it by the vertex offset amount.

I then extrude the vertex towards its normal vector by the amount calculated.

Finally, in line 95 I use the “TRANSFORM_TEX” macro again to store the modified UV coordinates for the displacement guide texture in the “displUV” field.

## Fragment shader

It’s finally time for the cool stuff! This is definitely one of the cases where the magic happens in the fragment shader, so let’s dive on in!

First of all, in lines 103 & 104 I store the UV coordinates for the main texture and the displacement texture in local variables. Here it’s not too useful, but in the next part the usefulness of that will be more apparent. In lines 107 & 108 I add the panning to the UVs based on the “_PanningSpeed” property. The x and y values of the vector correspond to the horizontal and vertical axes of the main texture’s UV coordinates respectively, while the z and w correspond to the horizontal and vertical axes of the displacement guide texture’s UV coordinates respectively.

In lines 111 and 112 I calculate the displacement that will be applied on the main texture. The concept is exactly the same as the one on the [displacement post](https://halisavakis.com/my-take-on-shaders-waving-displacement/): I sample the displacement guide, keep the red and green value (which correspond to the horizontal and vertical axis respectively), I then map the value to a [-1, 1] range and multiply it by the displacement amount.

The actual sampling of the main texture happens in line 115. There I sample the “_MainTex” while also adding the displacement to the corresponding UV coordinates. I lerp the value with 0.5 based on the value of “_Contrast” to adjust the contrast of the texture, and then I use “pow” to raise the value to a power specified via the “_Power” property. Playing around with these field will give you a more clear picture of what they do.

In lines 118-119 I take care of the clipping. For this I use the value from the texture and subtract “_Cutoff” from it. Note that the value I use is read from the main texture but it doesn’t correspond to the actual final color. If “_Cutoff” is larger than the current pixel’s value of “col”, then the pixel is discarded.

The actual coloring takes place in lines 121 & 122. Firstly, I sample the “_GradientMap” texture using the value of the main texture as the horizontal coordinate for the UVs. The final color is then determined as such:

- I multiply the RGB values of the gradient map by the RGB values of the “_Color” property and the alpha channel of the gradient map.
- The alpha channel of the color stays at 1, so the alpha channel of the gradient map is actually used to determine the brightness of a specific value and give a variation to the emissive values of the material.
- To the resulting color I add the “_BurnColor” which is multiplied by 2 factors
    - 0 or 1 depending on whether the “_BurnSize” is larger than “test” or not, so as to only affect an area around the edges of the dissolved pixels.
    - A number from 0 to 1 depending on whether the “_Cutoff” value is larger than 0.1, smaller than 0.001, or anywhere in between. This is to ensure that the burning will actually occur if the “_Cutoff” value is above 0 and I’m using smoothstep instead of step so that the transition is smoother and the burning doesn’t just “snap”.

I apply the fog to the final color and then return it, and that’s about it!

## Conclusion

This is a fairly easy and beginner- friendly tutorial, I think. The effects demonstrated are quite popular and easy to recreate, but I believe that the value of this shader is that one can make a lot of cool effects by combining all these features. With the effects we’ll add in the next posts, the possibilities get even more.

I hope you have as much fun with this shader as I did (and still am). I’ll see you in the [next one](https://halisavakis.com/my-take-on-shaders-vfx-master-shader-part-ii/)!