---
layout: single
title: Ch2. The Graphics Rendering Pipeline
date: 2023-03-07 10:32:19 +0800
excerpt: 筆記
categories:
- 筆記
tags:
- Render
- Note
mermaid: true
---

# 渲染流程 Rendering Pipeline
## 概覽
給予物件、光源、相機、繪製出圖形。
![](/assets/imgs/Notes/RealTimeRendering/ch2/CameraAndObjects.png)
* 展示相機顯示物件的示例圖，只有範圍(view volume)內的物件能夠看到。

![](/assets/imgs/Notes/RealTimeRendering/ch2/Pipeline.png)
* 渲染流程分為四個階段，每個階段還能各自有自己的Pipeline，即有更細的子階段。

* 渲染速度計算: frames per second(FPS)，即每秒渲染幾張圖片。

* [Application(應用階段)](#application-應用階段)
    > 由應用程式負責，在CPU上執行。諸如碰撞偵測、動畫、物理模擬...等
* [Geometry Processing(幾何處理)](#geometry-processing-幾何處理)
    > 幾何相關，如變換、投影...等，此階段負責要繪製什麼、怎麼繪製、繪製在哪裡，通常由GPU執行。
* [Rasterization Stage(光柵化階段)](#rasterization-stage-光柵化階段)
    > GPU，輸入頂點、形成三角形、找到所有三角形內的像素，傳給下一個階段。
* [Pixel Processing(像素處理)](#pixel-processing-像素處理)
    > GPU，處理每個像素，計算顏色、深度...等。

</br>

## Application 應用階段
&emsp;&emsp;由應用程式負責，在CPU上執行。諸如碰撞偵測、動畫、物理模擬...等。
&emsp;&emsp;在CPU執行，開發者可以完全決定這個階段要做什麼，也會影響效率。此外，部分程式能透過 *compute shader* 來讓GPU執行解此提升效率。  
&emsp;&emsp;這個階段沒有子階段，但為了提升效能，能夠透過CPU多核執行來處理。

## Geometry Processing 幾何處理
&emsp;&emsp;幾何相關，如變換、投影...等，此階段負責要繪製什麼、怎麼繪製、繪製在哪裡，通常由GPU執行。
&emsp;&emsp;逐頂點執行、逐三角形執行。此階段還會再細分成四個子階段。
![](/assets/imgs/Notes/RealTimeRendering/ch2/Geometry.png)

### 頂點著色 Vertex Shading, 投影 Projecttion

> &emsp;&emsp;用於計算頂點位置以及決定頂點資料如何輸出，如法線或貼圖座標。這個可程式化的頂點處理稱為 *頂點著色器 (vertex shader)* 。  
  
> &emsp;&emsp;空間變換
> * *模型空間(model space)* 以模型中心為原點的空間
> * *世界空間(world space)* 世界中獨一無二的座標
> * *相機空間(camera space)* 以相機為原點的空間
> * *裁剪空間(clip space)* 在相機視錐體內辨別可不可見，經過投影後的空間
> * 一個模型一開始位於自己的 *模型空間(model space)* 中，當頂點和法線經過 *變換(transform)* 後，會來到 *世界空間(world space)* 。接著為了投影和剪裁，相機和模型會套用 *視圖變換 (view transform)*，來讓相機位於原點的位置，此時空間為 *相機空間(camera space, view space, eye space)* 。 相機的可視範圍為一個視錐體，為了辨別物體可不可見，再對模型套用投影而來到 *裁剪空間(clip space)*
![](/assets/imgs/Notes/RealTimeRendering/ch2/Transform.png)

> &emsp;&emsp; Shading  
> &emsp;&emsp;除了形狀和位置，材質和光源也很重要，決定光在材質上的效果的方法稱為 *shading*，包含了在物件的每個頂點計算 *shading equation*。  
> &emsp;&emsp;作為頂點著色的一部分，渲染系統會處理 *投影(porjection)* 和 *剪裁(clipping)* 。將 view volume 轉換為位於 *(-1, -1, -1) ~ (1, 1, 1)* 內的單位立方體，稱為 *canonical view volume*。  

> &emsp;&emsp; Porjection  
> &emsp;&emsp;投影會在 GPU 以及藉由 vertex shader 最先完成，分為 *正交(orthographic)* 及 *透視(perspective)* 兩種方法。
![](/assets/imgs/Notes/RealTimeRendering/ch2/Projection.png)  
> * 正交投影的 view volume 為一個長方體，其特色是物體遠近不受影響。
> * 透視投影中，距離相機越遠的物體，會看起來越小。此時 view volume 稱為 *frustum*，會是一個長方形底的角錐體。  
經過投影後，物體就位於 *裁剪座標(clip coordinate)*  
  
###  Optional Vertex Processing  
  


## Rasterization Stage 光柵化階段
## Pixel Processing 像素處理
 


