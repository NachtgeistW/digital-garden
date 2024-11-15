# My take on shaders: Stylized water shader

By [Harry Alisavakis](https://halisavakis.com/author/tofa/ "Posts by Harry Alisavakis") on [Thursday, September 10, 2020](https://halisavakis.com/2020/09/)

[![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/screen_3840x2160_2020-09-10_17-30-01.png?fit=780%2C439&ssl=1)](https://halisavakis.com/my-take-on-shaders-stylized-water-shader/ "Permalink to: "My take on shaders: Stylized water shader"")

## Patrons

This tutorials is brought to you by these amazing Patrons:

- orels1
- Tiph’ (DN)
- Not Invader Zim

## Introduction

By now you’ve already figured out that I’m quite fond of water shaders; both seeing them and making them. I have around 3-4 different water shader versions on my playground project and whenever I get new ideas to test out, I’ll make 3-4 more.

Now, I’ve already covered a water shader in [this older tutorial](https://halisavakis.com/my-take-on-shaders-water-shader/) though that one tends to be closer to a “realistic” look (even though it’s not really realistic) and features a bunch of bells and whistles. This shader, on the other hand, produces a simpler, more stylized water effect and, while it includes techniques from the previous water shader, it also introduces some other elements that could potentially inspire you for other effects as well.

## Shader features

This shader is a surface shader that once again uses that depth difference technique to calculate the intersection at three levels: the shoreline, the in-between intersection and the deep-water fog. This was designed to give us some nice color transitions and allow for more control over the color of the water based on depth.

There’s no vertex displacement, tessellation or height maps for this shader; it’s designed to be placed on a flat quad/plane and only uses two world-space-mapped normal maps that are blended together for more variety, but that’s about it.

Also, this shader has support for dynamic water ripples which use a separate orthograpic camera, a render texture and particles! Will also cover that here, but the core principle is the same as the one shown in [Minionsart’s](https://twitter.com/minionsart) [interactive water tutorial](https://www.patreon.com/posts/24192529).

## The (shader) code

Here’s the code for the shader:

[?](https://halisavakis.com/my-take-on-shaders-stylized-water-shader/?continueFlag=9ffcc588f4033ae4c4f9e3d6ee98c0f0#)

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39<br><br>40<br><br>41<br><br>42<br><br>43<br><br>44<br><br>45<br><br>46<br><br>47<br><br>48<br><br>49<br><br>50<br><br>51<br><br>52<br><br>53<br><br>54<br><br>55<br><br>56<br><br>57<br><br>58<br><br>59<br><br>60<br><br>61<br><br>62<br><br>63<br><br>64<br><br>65<br><br>66<br><br>67<br><br>68<br><br>69<br><br>70<br><br>71<br><br>72<br><br>73<br><br>74<br><br>75<br><br>76<br><br>77<br><br>78<br><br>79<br><br>80<br><br>81<br><br>82<br><br>83<br><br>84<br><br>85<br><br>86<br><br>87<br><br>88<br><br>89<br><br>90<br><br>91<br><br>92<br><br>93<br><br>94<br><br>95<br><br>96<br><br>97<br><br>98<br><br>99<br><br>100<br><br>101<br><br>102<br><br>103<br><br>104<br><br>105<br><br>106<br><br>107<br><br>108<br><br>109<br><br>110<br><br>111<br><br>112<br><br>113<br><br>114<br><br>115<br><br>116<br><br>117<br><br>118<br><br>119<br><br>120<br><br>121<br><br>122<br><br>123<br><br>124|`Shader` `"Custom/StylizedWater"`<br><br>`{`<br><br>    `Properties`<br><br>    `{`<br><br>        `[Header(Colors)]`<br><br>        `[HDR]_Color (``"Color"``, Color) = (1,1,1,1)`<br><br>        `[HDR]_FogColor(``"Fog Color"``, Color) = (1,1,1,1)`<br><br>        `[HDR]_IntersectionColor(``"Intersection color"``, Color) = (1,1,1,1)`<br><br>        `[Header(Thresholds)]`<br><br>        `_IntersectionThreshold(``"Intersction threshold"``,` `float``) = 0`<br><br>        `_FogThreshold(``"Fog threshold"``,` `float``) = 0`<br><br>        `_FoamThreshold(``"Foam threshold"``,` `float``) = 0`<br><br>        `[Header(Normal maps)]`<br><br>        `[Normal]_NormalA(``"Normal A"``, 2D) =` `"bump"` `{}`<br><br>        `[Normal]_NormalB(``"Normal B"``, 2D) =` `"bump"` `{}`<br><br>        `_NormalStrength(``"Normal strength"``,` `float``) = 1`<br><br>        `_NormalPanningSpeeds(``"Normal panning speeds"``, Vector) = (0,0,0,0)`<br><br>        `[Header(Foam)]`<br><br>        `_FoamTexture(``"Foam texture"``, 2D) =` `"white"` `{}`<br><br>        `_FoamTextureSpeedX(``"Foam texture speed X"``,` `float``) = 0`<br><br>        `_FoamTextureSpeedY(``"Foam texture speed Y"``,` `float``) = 0`<br><br>        `_FoamLinesSpeed(``"Foam lines speed"``,` `float``) = 0`<br><br>        `_FoamIntensity(``"Foam intensity"``,` `float``) = 1`<br><br>        `[Header(Misc)]`<br><br>        `_RenderTexture(``"Render texture"``, 2D) =` `"black"` `{}`<br><br>        `_Glossiness (``"Smoothness"``, Range(0,1)) = 0.5`<br><br>        `_FresnelPower(``"Fresnel power"``,` `float``) = 1`<br><br>    `}`<br><br>    `SubShader`<br><br>    `{`<br><br>        `Tags {` `"RenderType"``=``"Transparent"` `"Queue"``=``"Transparent"` `}`<br><br>        `Blend SrcAlpha OneMinusSrcAlpha`<br><br>        `ZWrite Off`<br><br>        `LOD 200`<br><br>        `CGPROGRAM`<br><br>        `#pragma surface surf Standard fullforwardshadows alpha:premul`<br><br>        `#pragma target 3.0`<br><br>        `struct` `Input`<br><br>        `{`<br><br>            `float4 screenPos;`<br><br>            `float3 worldPos;`<br><br>            `float3 viewDir;`<br><br>        `};`<br><br>        `fixed4 _Color;`<br><br>        `fixed4 _FogColor;`<br><br>        `fixed4 _IntersectionColor;`<br><br>        `float` `_IntersectionThreshold;`<br><br>        `float` `_FogThreshold;`<br><br>        `float` `_FoamThreshold;`<br><br>        `sampler2D _NormalA;`<br><br>        `sampler2D _NormalB;`<br><br>        `float4 _NormalA_ST;`<br><br>        `float4 _NormalB_ST;`<br><br>        `float` `_NormalStrength;`<br><br>        `float4 _NormalPanningSpeeds;`<br><br>        `sampler2D _FoamTexture;`<br><br>        `float4 _FoamTexture_ST;`<br><br>        `float` `_FoamTextureSpeedX;`<br><br>        `float` `_FoamTextureSpeedY;`<br><br>        `float` `_FoamLinesSpeed;`<br><br>        `float` `_FoamIntensity;`<br><br>        `sampler2D _RenderTexture;`<br><br>        `half _Glossiness;`<br><br>        `float` `_FresnelPower;`<br><br>        `sampler2D _CameraDepthTexture;`<br><br>        `float3 _CamPosition;`<br><br>        `float` `_OrthographicCamSize;`<br><br>        `// Add instancing support for this shader. You need to check 'Enable Instancing' on materials that use the shader.`<br><br>        `// See [https://docs.unity3d.com/Manual/GPUInstancing.html](https://docs.unity3d.com/Manual/GPUInstancing.html) for more information about instancing.`<br><br>        `// #pragma instancing_options assumeuniformscaling`<br><br>        `UNITY_INSTANCING_BUFFER_START(Props)`<br><br>            `// put more per-instance properties here`<br><br>        `UNITY_INSTANCING_BUFFER_END(Props)`<br><br>        `void` `surf (Input IN, inout SurfaceOutputStandard o)`<br><br>        `{`<br><br>            `float2 rtUV = IN.worldPos.xz - _CamPosition.xz;`<br><br>            `rtUV = rtUV/(_OrthographicCamSize *2);`<br><br>            `rtUV += 0.5;`<br><br>            `fixed4 rt = tex2D(_RenderTexture, rtUV);`<br><br>            `float` `depth = tex2Dproj(_CameraDepthTexture, UNITY_PROJ_COORD(IN.screenPos));`<br><br>            `depth = LinearEyeDepth(depth);`<br><br>            `float` `fogDiff = saturate((depth - IN.screenPos.w) / _FogThreshold);`<br><br>            `float` `intersectionDiff = saturate((depth - IN.screenPos.w) / _IntersectionThreshold);`<br><br>            `float` `foamDiff = saturate((depth - IN.screenPos.w) / _FoamThreshold);`<br><br>            `foamDiff *= (1.0 - rt.b);`<br><br>            `fixed4 c = lerp(lerp(_IntersectionColor, _Color, intersectionDiff), _FogColor, fogDiff);`<br><br>            `float` `foamTex = tex2D(_FoamTexture, IN.worldPos.xz * _FoamTexture_ST.xy + _Time.y * float2(_FoamTextureSpeedX, _FoamTextureSpeedY));`<br><br>            `float` `foam = step(foamDiff - (saturate(sin((foamDiff - _Time.y * _FoamLinesSpeed) * 8 * UNITY_PI)) * (1.0 - foamDiff)), foamTex);`<br><br>            `float` `fresnel = pow(1.0 - saturate(dot(o.Normal, normalize(IN.viewDir))), _FresnelPower);`<br><br>            `o.Albedo = c.rgb;`<br><br>            `float3 normalA = UnpackNormalWithScale(tex2D(_NormalA, IN.worldPos.xz * _NormalA_ST.xy + _Time.y * _NormalPanningSpeeds.xy + rt.rg), _NormalStrength);`<br><br>            `float3 normalB = UnpackNormalWithScale(tex2D(_NormalB, IN.worldPos.xz * _NormalB_ST.xy + _Time.y * _NormalPanningSpeeds.zw + rt.rg), _NormalStrength);`<br><br>            `o.Normal = normalA + normalB;`<br><br>            `o.Smoothness = _Glossiness;`<br><br>            `o.Alpha = lerp(lerp(c.a * fresnel, 1.0, foam), _FogColor.a, fogDiff);`<br><br>            `o.Emission = foam * _FoamIntensity;`<br><br>        `}`<br><br>        `ENDCG`<br><br>    `}`<br><br>    `FallBack` `"Diffuse"`<br><br>`}`|

### Properties

|   |   |
|---|---|
|**Name**|**Description**|
|_Color|The color of the water when it’s not too close to the shore and not deep enough.|
|_FogColor|The color of the water when it’s deep enough.|
|_IntersectionColor|The color of the water close to the shore.|
|_IntersectionThreshold|The threshold that determines the size of the area close to the shore that will be colored as defined by “_IntersectionColor”.|
|_FogThreshold|The threshold that determines the depth at which the water will be colored as defined by “_FogColor”.|
|_FoamThreshold|The threshold that determines how far away from the shore the water will have foam/shorelines.|
|_NormalA|The first normal map texture.|
|_NormalB|The second normal map texture.|
|_NormalStrength|The strength of the normals (including those from the ripples).|
|_NormalPanningSpeeds|The panning speeds for each of the two normal maps. This is a vector4 field where:  <br>X – The speed of the NormalA texture in the X axis.  <br>Y – The speed of the NormalA texture in the Y axis.  <br>Z – The speed of the NormalB texture in the X axis.  <br>W – The speed of the NormalB texture in the Y axis.|
|_FoamTexture|The noise texture to be added to give some variation to the shoreline foam.|
|_FoamTextureSpeedX|The panning speed of the foam texture in the X axis.|
|_FoamTextureSpeedY|The panning speed of the foam texture in the Y axis.|
|_FoamLinesSpeed|The speed at which the lines of the foam move inwards.|
|_FoamIntensity|The intensity of the foam effects; mostly to be used with a bloom post-processing effect.|
|_RenderTexture|The render texture containing information about the water ripples.|
|_Glossiness|The smoothness of the water surface.|
|_FresnelPower|The power of the fresnel mask used to adjust the water’s transparency based on view direction.|

### The in-between stuff

First off, as you can imagine, this shader is for transparent objects, so lines 35-37 include the classic directives etc to declare that. This, however, is a surface shader so there’s an extra step we need to make transparency working.

In line 41 you’ll notice the “alpha:premul” at the end. You might have seen other transparent surface shaders that instead use “alpha:fade”. This is the different transparency mode that’s also offered in Unity’s standard shader.

![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/image.png?resize=331%2C118&ssl=1)

In the standard shader, the difference between “Fade” and “Transparent” is that when the material is set to “Fade”, elements like specular reflections or colors from reflection probes get faded out with alpha. On the contrary, “Trasparent” mode allows these elements to stay in even if your albedo color is completely clear.

That’s the difference between “alpha:fade” and “alpha:premul”. You’d expect the second mode to be called “alpha:transparent” but it’s “alpha:premul” instead.

![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/image-3.png?resize=795%2C449&ssl=1)

The shader with “alpha:fade”

![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/image-4.png?resize=797%2C450&ssl=1)

The shader with “alpha:premul”. Notice the specular reflection next to the right box.

In the input struct we don’t need a whole lot of stuff, just the screen-space position for the depth difference, the world-space position for some planar texture mapping and the view direction for a fresnel effect to adjust the transparency. I’m not using any UV coordinates as I’ve chosen to map all textures using world-space position instead to help with tiling etc.

Lines 53-77 is just about redeclaring all the properties above, and in lines 79-81 I also declare some fields that aren’t featured in the properties block:

- _CameraDepthTexture: The depth texture of the camera, used to get the the depth difference for the intersection effects.
- _CamPosition: The position of the orthographic camera that’s used for the water ripples. This is passed to the shader through a script.
- _OrthographicCamSize: The orthographic size of the aforementioned camera. Again, the value for this field is set by a script.

### The surf method ![🏄‍♀️](https://s.w.org/images/core/emoji/14.0.0/svg/1f3c4-200d-2640-fe0f.svg)

First off, in lines 93-96 I get the color of the render texture upon which the orthographic camera for the water ripples writes. The first three lines are just about getting proper UVs to sample the render texture. You can imagine this process as projecting what the orthograpic camera sees onto the plain, in the exact size and position of the camera’s frustum.

For that reason, we need to get a vector from the camera’s position to the pixel’s position in world-space. We only care about the X and Z axes so we don’t take Y into account. The vector is then divided by the width of the camera’s frustum (which equals to two times its orthographic size) so we eventually get values going from 0 to 1 to properly sample the render texture. The UVs then get increased by 0.5 so they can be properly centered.

Lines 98 and 99 are about getting the camera’s linear eye space depth, so I can use it for the different intersections.

In lines 101-103 I get the depth differences for the fog color, the intersection color and the shore foam respectively. I use the same process of subtracting the world-space view depth (the w component of the screen position field) from the camera’s depth value and then dividing the whole thing by a different threshold for different effects.

In line 104 I also multiply the field for the shore foam depth difference by the inverse of the render texture’s blue channel in order to add some more foam based where the water ripples spawn, for visual flare.

In line 106 I interpolate between the normal color, the intersection color and the fog color based on the respective depth difference values to get the final albedo color.

Lines 108 and 109 are where I calculate the foam for the shore and the ripples. First I sample the noise texture I use for some variation on the foam. I use the x and z components of the world space position and multiply them by the “_FoamTexture_ST.xy” which is the scaling of the texture that can be adjusted via the material inspector. I also offset the foam noise texture over time based on “_FoamTextureSpeedX” and “_FoamTextureSpeedY”.

Line 109 is a bit interesting; normally I’d be doing something like “step(foamTex, foamDiff)” which would give a result like this:

![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/image-5.png?resize=523%2C311&ssl=1)

Which is nice and cool and all that, but I wanted to actually have some lines going inwards that get distorted with the noise texture and have a more stylized look overall, which is why I harnessed the power of the sine wave.

Let’s break down what’s going on here:

“foamDiff” is a value going from 0 to 1, where 0 is where the water touches the shore and then it interpolates to 1 as we get away from the shore. Adjusting the “_FoamThreshold” increases or decreases the area that “foamDiff” interpolates from 0 to 1. If we use a sine method with “foamDiff” as input we can get a result like that:

![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/image-6.png?resize=561%2C349&ssl=1)

This is done with this bit of code:

foam = saturate(sin((foamDiff - _Time.y * _FoamLinesSpeed) * 8 * UNITY_PI));

Basically, I’m offsetting “foamDiff” over time, multiplying it by 8π (or 4τ) , passing it through a sine method (making the lines going inwards), and clamping that from 0 to 1. We get 4 lines cause with a period of 4τ you get 4 times where you get a value of 1 with an input value going from 0 to 1. I’ve hardcoded it to 8 as I saw it worked best, but you can change it or expose it to a property.

Since “foamDiff” is 1 for the rest of the body of water, as the sine wave moves we get this annoying effect:

![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/image-7.png?resize=608%2C217&ssl=1)

To counter that, I subtract the whole sine thing from “foamDiff” after I’ve first multiplied it with 1 – foamDiff to restrict it in the area around the shoreline. This was after a bit of trial and error but it both gets rid of the effect above and also gives some more noise right on the shore, looking like this:

![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/image-8.png?resize=566%2C329&ssl=1)

In line 111 I just calculate a bit of a fresnel mask to be used with transparency. This is just as a small detail to make the water more transparent when you’re looking straight down at it.

In line 113 I assign the rgb value of the color to the albedo color and in lines 114 and 115 I sample the two normal maps with their corresponding panning speeds and unpack them using “_NormalStrength” as the scale, to adjust their strength. Notice that I’m also displacing the UVs using the red and green channel of the render texture color; that’s so that the ripples can displace the normals of the water surface.

Then, in line 116 I add the two values from the normal maps together and I assign the result to the “Normal” field of the “SurfaceOutputStandard” object.

In line 117 I just assign the smoothness value and in line 118 I calculate the transparency of the water. I basically want to take into account the fresnel, the alpha from the different colors and the deeper areas (the “fog”) as these should work a bit independently. Firstly, I didn’t want any transparency on the places where there’s foam, so after I multiply the alpha channel of the color with the fresnel mask, I interpolate between that value and 1 using the foam as a parameter, so the alpha would be 1 where there’s foam. Then, I interpolate between that alpha value and the fog’s color alpha channel based on “fogDiff”, so that I can have no transparency in deeper waters.

Finally, in line 119 I apply the foam to the emission field, after I multiply it by “_FoamIntensity”. Here I assume that the foam will just be white (that can also be emissive and glowing) but you can add a color there if you want.

## Water ripples

The water ripples effect needs some specific setup to work along with a small custom C# script. I’ll include a unity package with a small demo scene in the end of the post so that you can see how everything is set up.

Firstly, you’re going to need an orthographic camera looking towards the surface of the water. This camera should render only what’s on a specific layer, I’m using the “water” layer.

![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/image-9.png?resize=456%2C352&ssl=1)

That camera should write on a square render texture which will be passed on to the stylized water shader.

The orthographic camera should also have this script attached to it:

[?](https://halisavakis.com/my-take-on-shaders-stylized-water-shader/?continueFlag=9ffcc588f4033ae4c4f9e3d6ee98c0f0#)

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19|`using` `System.Collections;`<br><br>`using` `System.Collections.Generic;`<br><br>`using` `UnityEngine;`<br><br>`[RequireComponent(``typeof``(Camera))]`<br><br>`public` `class` `WaterRippleCamera : MonoBehaviour {`<br><br>    `private` `Camera cam;`<br><br>    `public` `MeshRenderer waterPlane;`<br><br>    `private` `void` `Awake() {`<br><br>        `cam = GetComponent<Camera>();`<br><br>    `}`<br><br>    `private` `void` `Update() {`<br><br>        `waterPlane.sharedMaterial.SetVector(``"_CamPosition"``, transform.position);`<br><br>        `waterPlane.sharedMaterial.SetFloat(``"_OrthographicCamSize"``, cam.orthographicSize);`<br><br>    `}`<br><br>`}`|

This script just passes the position of the camera and its orthographic size to the material of the water plane.

For the water ripples I’m using a particle system that has a material with this custom shader:

[?](https://halisavakis.com/my-take-on-shaders-stylized-water-shader/?continueFlag=9ffcc588f4033ae4c4f9e3d6ee98c0f0#)

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39<br><br>40<br><br>41<br><br>42<br><br>43<br><br>44<br><br>45<br><br>46<br><br>47<br><br>48<br><br>49<br><br>50<br><br>51<br><br>52<br><br>53<br><br>54<br><br>55<br><br>56<br><br>57<br><br>58<br><br>59<br><br>60<br><br>61<br><br>62<br><br>63<br><br>64<br><br>65<br><br>66<br><br>67<br><br>68<br><br>69|`Shader "Unlit/WaterRipples"`<br><br>`{`<br><br>    `Properties`<br><br>    `{`<br><br>        `[Normal]_MainTex ("Texture", 2D) = "bump" {}`<br><br>        `_MaskRadiusOuter("Mask radius outer", Range(0,.5)) = 0`<br><br>        `_MaskRadiusInner("Mask radius inner", Range(0,.5)) = 0`<br><br>        `_MaskSoftnessOuter("Mask softness outer", Range(0,1)) = 0`<br><br>        `_MaskSoftnessInner("Mask softness inner", Range(0,1)) = 0`<br><br>    `}`<br><br>    `SubShader`<br><br>    `{`<br><br>        `Tags { "RenderType"="Transparent" "Queue"="Transparent" "PreviewType"="Plane"}`<br><br>        `Blend SrcAlpha OneMinusSrcAlpha`<br><br>        `ZWrite Off`<br><br>        `LOD 100`<br><br>        `Pass`<br><br>        `{`<br><br>            `CGPROGRAM`<br><br>            `#pragma vertex vert`<br><br>            `#pragma fragment frag`<br><br>            `#include "UnityCG.cginc"`<br><br>            `struct appdata`<br><br>            `{`<br><br>                `float4 vertex : POSITION;`<br><br>                `float2 uv : TEXCOORD0;`<br><br>                `float4 color : COLOR;`<br><br>            `};`<br><br>            `struct v2f`<br><br>            `{`<br><br>                `float2 uv : TEXCOORD0;`<br><br>                `float4 vertex : SV_POSITION;`<br><br>                `float4 color : COLOR;`<br><br>            `};`<br><br>            `sampler2D _MainTex;`<br><br>            `float4 _MainTex_ST;`<br><br>            `sampler2D _Mask;`<br><br>            `float _MaskSoftnessOuter;`<br><br>            `float _MaskRadiusOuter;`<br><br>            `float _MaskRadiusInner;`<br><br>            `float _MaskSoftnessInner;`<br><br>            `v2f vert (appdata v)`<br><br>            `{`<br><br>                `v2f o;`<br><br>                `o.vertex = UnityObjectToClipPos(v.vertex);`<br><br>                `o.uv = TRANSFORM_TEX(v.uv, _MainTex);`<br><br>                `o.color = v.color;`<br><br>                `return o;`<br><br>            `}`<br><br>            `fixed4 frag (v2f i) : SV_Target`<br><br>            `{`<br><br>                `float circleSDF = distance(i.uv, float2(0.5,0.5));`<br><br>                `float outerMask = 1.0 - smoothstep(_MaskRadiusOuter, saturate(_MaskRadiusOuter + _MaskSoftnessOuter), circleSDF);`<br><br>                `float innerMask = smoothstep(_MaskRadiusInner, saturate(_MaskRadiusInner + _MaskSoftnessInner), circleSDF);`<br><br>                `float mask = outerMask * innerMask;`<br><br>                `fixed4 col = fixed4(UnpackNormal(tex2D(_MainTex, i.uv)), mask) * i.color;`<br><br>                `return col;`<br><br>            `}`<br><br>            `ENDCG`<br><br>        `}`<br><br>    `}`<br><br>`}`|

I’m not going to do a detailed breakdown of this shader; it’s just taking a normal map texture and masks it in a ring fashion so that it only renders the normal ring instead of the whole quad. It also multiplies the final color with the vertex color, so that you can make them ripple particles fade over time.

For the ripples in the demo I use a normal map that looks like this:

![](https://i0.wp.com/halisavakis.com/wp-content/uploads/2020/09/normal_ring_1.png?resize=1024%2C1024&ssl=1)

Feel free to experiment with other textures as well though.

The particle system should be in the layer that the orthographic camera sees (“Water” in my case). It’s also a good idea to exclude that layer from your main camera, so your particles don’t get rendered as usual.

## The package

You can download a unity package with a demo scene to get a sense of the overall setup in this link:

[Stylized water demo scene](https://drive.google.com/file/d/1cLJlnCTbOEqBn_6_QMVau0Uyrs-6laZ2/view?usp=sharing)

## Conclusion

This is a very basic implementation of a stylized water effect, as you have probably guessed already. There’s a bunch than can be added on top, like refraction, chromatic aberration, caustics etc. Maybe I’ll cover some of these in a future post, but for now I mostly wanted a basic version that you can adjust as you please.

It’s worth noting that the whole setup with the orthographic camera and the particles can be used for many other effects as well; like vertex displacement on grass for example. It’s quite a versatile setup and I bet you can find some more cool uses for it!

See you in the [next one](https://halisavakis.com/my-take-on-shaders-sky-shader/)!!