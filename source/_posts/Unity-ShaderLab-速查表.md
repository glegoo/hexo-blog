---
title: Unity ShaderLab常用变量·语义·函数速查表
date: 2019-01-09 09:18:49
tags: 
    - Unity
    - Shader
comments: true
---

持续更新……

---
## Unity内置的变换矩阵
| 变量名        | 描述   |
| --------   | -----  |
| UNITY_MATRIX_MVP     | 当前的模型·观察·投影矩阵，用于将顶点/方向矢量从模型空间变换到裁剪空间 |
| UNITY_MATRIX_MV        |   当前的模型·观察矩阵，用于将顶点顶点/方向矢量从模型空间变换到观察空间   |
| UNITY_MATRIX_V        |   当前的观察矩阵，用于将顶点顶点/方向矢量从世界空间变换到观察空间   |
| UNITY_MATRIX_P        |   当前的观察矩阵，用于将顶点顶点/方向矢量从观察空间变换到裁剪空间   |
| UNITY_MATRIX_VP        |   当前的观察·投影矩阵，用于将顶点顶点/方向矢量从世界空间变换到裁剪空间   |
| UNITY_MATRIX_T_MV        |   UNITY_MATRIX_MV 的转置矩阵   |
| UNITY_MATRIX_IT_MV        |   UNITY_MATRIX_MV 的逆转置矩阵，用于将法线从模型空间变换到观察空间，也可用于得到 UNITY_MATRIX_MV 的逆矩阵   |
| _Object2World        |   当前的模型矩阵，用于将顶点顶点/方向矢量从模型空间变换到世界空间   |
| _World2Object        |   _Object2World的逆矩阵，用于将顶点顶点/方向矢量从世界空间变换到模型空间   |
<!-- more -->

## Unity 内置的摄像机和屏幕参数
| 变量名     | 类型   |   描述   |
| --------   | -----  | -----  |
| _WorldSpaceCameraPos | float3 | 该摄像机在世界空间中的位置 |
| _ProjectionParams | float4 | x = 1.0 (或-1.0，如果正在使用一个翻转的投影矩阵进行渲染)，y = Near，z = Far，w = 1.0 + 1.0/Far，其中 Near 和 Far 分别是近裁剪平面和远裁剪平面和摄像机的距离 |
| _ScreenParams | float4 | x = width，y = height，z = 1.0 + 1.0/width，w = 1.0 + 1.0/height，其中 width 和 height 分别是该摄像机的渲染目标( render target )的像素宽度和高度 |
| _ZBufferParams | float4 | x = 1 - Far/Near，y = Far/Near，z = x/Far，w = y/Far，该变量用于线性化Z缓存中的深度值 |
| unity_OrthoParams | float4 | x = width，y = height，z没有定义，w = 1.0（该摄像机是正交摄像机）或 w = 0.0（该摄像机是透视摄像机），其中 width 和 height 是正交投影摄像机的宽度和高度 |
| unity_CameraProjection | float4x4 | 该摄像机的投影矩阵 |
| unity_CameraInvProjection | float4x4 | 该摄像机的投影矩阵的逆矩阵 |
| unity_CameraWorldClipPlanes[6] | float4 | 该摄像机的6个裁剪平面在世界空间下的等式，按如下顺序：左、右、下、上、近、远裁剪平面 |

## ShaderLab 中属性的类型和 CG 变量类型的匹配关系
| ShaderLab 属性类型        | CG 变量类型   |
| --------   | -----  |
| Color, Vector   | float4, half4, fixed4  |
| Range, Float   | float, half, fixed  |
| 2D   | sampler2D  |
| Cube   | samplerCube  |
| 3D   | sampler3D  |

## Unity 中一些常用的包含文件
| 文件名       | 描述   |
| --------   | -----  |
| UnityCG.cginc  | 包含了最常使用的帮主函数、宏和结构体等  |
| UnityShaderVariables.cginc  | 在编译 Unity Shader 时，会被自动包含金来，包含了许多内置的全局变量，如UNITY_MATRIX_MVP等  |
| Lighting.cginc  | 包含了各种内置的光照模型，如果编写的是 Surface Shader 的话，会自动包含进来  |
| HLSLSupport.cginc  | 在编译 Unity Shader 时，会被自动包含进来，生命了很多用于跨平台编译的宏和定义  |

## UnityCG.cginc 中的一些常用的结构体
| 名称     | 描述   |   包含的变量   |
| --------   | -----  | -----  |
| appdata_base | 可用于顶点着色器的输入 | 顶点位置、顶点法线、第一组纹理坐标 |
| appdata_tan | 可用于顶点着色器的输入 | 顶点位置、顶点切线、顶点法线、第一组纹理坐标 |
| appdata_full | 可用于顶点着色器的输入 | 顶点位置、顶点切线、顶点法线、四组（或更多）纹理坐标 |
| appdata_img | 可用于顶点着色器的输入 | 顶点位置、第一组纹理坐标 |
| v2f_img | 可用于顶点着色器的输入 | 裁剪空间中的位置、纹理坐标 |

## UnityCG.cginc 中的一些常用的帮助函数
| 函数名       | 描述   |
| --------   | -----  |
| float3 WorldSpaceViewDir (float4 v)  | 输入一个模型空间中的顶点位置，返回世界空间中从该点到摄像机的观察方向  |
| float3 ObjSpaceViewDir (float4 v)  | 输入一个模型空间中的顶点位置，返回模型空间中从该点到摄像机的观察方向  |
| float3 WorldSpaceLightDir (float4 v)  | **仅可用于前向渲染中**，输入一个模型空间中的顶点位置，返回世界空间中从该点到光源的光照方向，没有被归一化  |
| float3 ObjSpaceLightDir (float4 v)  | **仅可用于前向渲染中**，输入一个模型空间中的顶点位置，返回模型空间中从该点到光源的光照方向，没有被归一化  |
| float3 UnityObjectToWorldNormal (float3 norm)  | 把法线方向从模型空间转换到世界空间中  |
| float3 UnityObjectToWorldDir (float3 norm)  | 把方向矢量从模型空间变换到世界空间中  |
| float3 UnityWorldToObjectDir (float3 norm)  | 把方向矢量从世界空间变换到模型空间中  |

## 从应用阶段传递模型数据给顶点着色器时 Unity 支持的常用语义
| 语义       | 描述   |
| --------   | -----  |
| POSITION | 模型空间中的顶点位置，通常是 float4 类型  |
| NORMAL | 顶点法线，通常是 float3 类型  |
| TANGENT | 顶点切线，通常是 float4 类型  |
| TEXCOORDn，如 TEXCOORD0、TEXCOORD1 | 该顶点的纹理坐标，TEXCOORD0 表示第一组纹理坐标，以此类推，通常是 float2 或 float4 类型  |
| COLOR | 顶点颜色，通常是 fixed4或float4 类型  |

## 从顶点着色器传递数据给片元着色器时 Unity 使用的常用语义
| 语义       | 描述   |
| --------   | -----  |
| SV_POSITION | 裁剪空间中的顶点坐标，结构体重必须包含一个用该语义修饰的变量。等同于 DirectX 9 中的 POSITION，但最好使用 SV_POSITION  |
| COLOR0 | 通常用于输出第一组顶点颜色，但不是必需的  |
| COLOR1 | 通常用于输出第二组顶点颜色，但不是必需的  |
| TEXCOORD0~TEXCOORD7 | 通常用于输出纹理坐标，但不是必需的  |

## 片元着色器输出时 Unity 支持的常用语义
| 语义       | 描述   |
| --------   | -----  |
| SV_Target | 输出值将会存储到渲染目标 (render target) 中。等同于 DirectX 9 中的 COLOR语义，但最好使用 SV_Target |

---
### 参考文献
1. 冯乐乐，《Unity Shader入门精要》