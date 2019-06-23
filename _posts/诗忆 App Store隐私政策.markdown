---
layout:     post
title:      "Shader学习笔记001"
subtitle:   "第一课"
date:       2018-04-13 17:49:00
author:     "Mas9urade"
header-img: "img/home-bg.jpg"
tags:
    - Shader
---

### 渲染流程分为三个阶段
1. 应用阶段（Application Stage); CPU实现
	1. 把数据加载到显存中；
		硬盘（HDD） -> RAM -> VRAM
	2. 设置渲染状态；
		使用哪种类型的着色器，光源属性，材质等；
	3. 调用Draw Call；
2. 几何阶段（Geometry Stage);   GPU实现
3. 光栅化阶段（Rasterizer Stage); GPU实现


### GPU渲染流水线
#### 1 . 几何阶段

#### **顶点数据 （显存）-> 顶点着色器 -> 曲面细分着色器 -> 几何着色器 -> 裁剪 -> 屏幕映射**

###### 顶点着色器(Vertex Shader）
完全可编程， 实现顶点的空间变化，顶点着色的功能；
1. 实现坐标变换，把顶点坐标从模型空间转换到齐次裁剪空间，之后使用透视除法，获得归一化的设备坐标（Normalized Device Coordinates);
2. 逐顶点光照，

##### 曲面细分着色器（Tessellation Shader)
可选着色器，用于细分图元；

##### 几何着色器(Geometry Shader)
可选着色器，可以被用于执行逐图元的着色操作，或者被用于产生更多的图元；

##### 裁剪（Clipping）
可配置，剔除不在摄像机视野内的顶点，剔除某些三角图元的面片；

##### 屏幕映射（Screen Mapping）
把每个图元的x，y坐标转换到屏幕坐标系；
注意：  OpenGL与DirectX的屏幕坐标系不同；

#### 2. 光栅化阶段

#### **屏幕映射 -> 三角形设置 -> 三角形遍历 -> 片元着色器 -> 逐片元操作 -> 屏幕图像**

##### 三角形设置（Triangle Setup)
计算光栅化一个三角形网络所需的信息，通过顶点计算三角形边界的表达方式；

##### 三角形遍历（Triangle Travelsal)
检查每个像素是否被一个三角网络覆盖，若被覆盖则生成一个片元（Fragment），最终输出片元序列（包含了最终颜色，屏幕坐标信息，深度信息，法线，纹理坐标等);

##### 片元着色器(Fragment Shader)
可编程着色器，在DirectX中也称像素着色器(Pixel Shader)，实质上是通过对顶点插值，通过颜色、纹理的计算，输出最终的颜色信息。

##### 逐片元操作（Per-Fragment Operations)

**片元 -> 模板测试 ->深度测试 -> 混合 -> 颜色缓冲区（帧缓冲区）**

OpenGL的说法，在DirectX中为输出合并阶段（Output-Merger）
1. 决定每个片元的可见性，涉及到了深度测试，模板测试等；
2. 若一个片元通过测试，就需要将该片元的颜色与存储在颜色缓冲区中的颜色进行混合。

##### 颜色缓冲区（帧缓冲区）
就是帧缓冲区（图形设备的内存），需要渲染的场景的每一个像素都最终写入该缓冲区，然后由他渲染到屏幕上显示。

##### 模板测试（Stencil Test)
~~GPU会首先模板缓冲区（Stencil Buffer)中该片元位置的模板值，将该值与读取到的参考值进行比较，进而修改模板缓冲区，....~~ 模板测试中可进行渲染阴影，轮廓渲染等。
 
##### 深度测试
主要影响透明效果

#### DrawCall相关
1. draw call 对帧率的影响在于cpu提交命令的速度;
2. 减少draw call需要避免使用大量很小的网格，避免使用过多的材质。


### 着色器介绍
```c
Shader
{
    subShader
	{
    	pass
    	{
        
    	}
	}
}
```
#### 1. 表面着色器（Surface Shader）
Unity特有的对于顶点/片元着色器Vertex/Fragment Shader的抽象封装，自动完成了光照细节的处理，但相应的渲染代价更大，性能更低。
写在SubShader内，不需要考虑pass的渲染问题。
#### 2.顶点/片元着色器(Vertex/Fragment Shader)
写在Pass内，可以通过选择pass控制渲染的实现细节；



| Albedo                                         | 反射率     | The Albedo parameter controls the base color of the shader surface, accepting either color values or texture maps. |
| :--------------------------------------------- | :--------- | ------------------------------------------------------------ |
| Normal                                         | 法线       | Normal maps are a special kind of texture that allow the addition of surface details, such as shading information transferred from highpoly meshes, bumps, grooves and scratches. You may connect normal maps or custom normal vectors here. |
| Metallic                                       | 金属的     | When working in the Metallic workflow, the reflectivity and light response of the surface are modified by both Metallic and Smoothness levels; both greyscale inputs. You may use a range of values from 0 to 1, affecting the whole surface, or even connect a texture, which will control the Metallic values while providing different values for different areas of the surface; 0 being dielectric ( non-metallic ) and 1 full Metal. |
| Occlusion                                      | 遮蔽       |                                                              |
| Emission                                       | 发散性     | Emission controls the colour and intensity of light emitted from the surface, regardless of the lighting conditions; accepts full RGB values. |
| Smoothness                                     | 平滑       | The concept of Smoothness applies to both Specular and Metallic workflows, and works very much the same way in both; also greyscale. A completely smooth surface at value 1 provides clear reflections, while a rough surface set to 0 creates a diffuse color with no clear reflections; It's also referred to as "Roughness" in some engines. |
| Ambient Occlusion                              | 环境光遮蔽 | The Occlusion input accepts an occlusion texture map, or custom value, that's used to provide information about which areas of the model should display high or low indirect lighting, which comes from ambient lighting and reflections. The occlusion map is a greyscale image where white indicates areas that should receive full indirect lighting, while black indicates no indirect lighting. |
| Transmission                                   |            | The Transmission is a highly optimized way of approximating light scattering. In other words, it defines how much light passes through a surface when lit from behind, which is ideal for less detailed assets such as leaves, cloth, or even wax objects; accepts full RGB inputs. |
| Translucency                                   | 半透明     | The Translucency input allows you to approximate SSS (Sub-Surface Scattering) effects in a simple and optimized manner by controlling the light-normal angle falloff offset. It is usually used for skin effects but flexible enough for other uses; accepts full RGB inputs. |
| Refraction                                     | 折射率     | The refraction input requires a shader with its Render Type set to Transparent and is used to simulate the deformation effect of objects seen through a medium such as glass or water, which is achieved by a screen-space UV offset that refracts the background pixels; accepts full RGB inputs. This technique is a simple approximation to a light phenomenon that often occurs when waves travel from a medium with a given refractive index to a medium with another at an oblique angle. |
| Opacity                                        | 不透明度   | The Opacity input requires a shader with its Render Type set to Transparent, being responsible for setting the transparency of a surface as a whole, using a range of values between 0 and 1, from completely transparent to fully opaque respectively; accepts full RGB inputs. |
| Opacity Mask                                   | 不透明蒙版 | The Opacity Mask requires a shader with its Render Type set to Transparent or a Masked Blend Mode, and works similarly to Opacity in the sense that it takes in a value between 0 and 1, from complete transparency to full opacity, but without considering the values in-between, resulting in either completely visible or completely invisible surfaces in specific areas. It is the perfect solution for materials that define complex solid surfaces such as wire mesh or chain link, as the opaque portions will still respect lighting; accepts greyscale inputs. |
| Custom Lighting                                | 特制光效   |                                                              |
| Local Vertex Offset ( Relative Vertex Output ) |            | The Local Vertex Offset input may be used to alter the shape of a surface through vertex manipulation, where a XYZ coordinate will define how each vertex will offset from its relative position. |
| Local Vertex Normal                            |            | The Local Vertex Normal allows the adjustment of the normal direction of any offset surfaces as Mesh Normals are not computed in real-time. This process is usually referred to as Normal Reconstruction. |
| Tessellation                                   | 图元镶嵌   | The Tessellation input allows the subdivision of a mesh's triangles, splitting them into smaller triangles at runtime in order to increase the surface detail of any given mesh. |
| Debug                                          |            | The Debug input generates a preview shader that overrides all other active inputs, drawing only what is plugged in to its input port. Please note that not all nodes, or specific combinations, can be previewed in the debug mode. |
|                                                |            |                                                              |
|                                                |            |                                                              |

http://wiki.amplify.pt/index.php?title=Unity_Products:Amplify_Shader_Editor/Standard_Surface_Output

###3D数学部分

####1. 左手系 与 右手系


