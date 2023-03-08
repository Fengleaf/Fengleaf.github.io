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

## 渲染流程 Rendering Pipeline
### 概覽
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

### Application 應用階段
&emsp;&emsp;由應用程式負責，在CPU上執行。諸如碰撞偵測、動畫、物理模擬...等。
&emsp;&emsp;在CPU執行，開發者可以完全決定這個階段要做什麼，也會影響效率。此外，部分程式能透過 *compute shader* 來讓GPU執行解此提升效率。  
&emsp;&emsp;這個階段沒有子階段，但為了提升效能，能夠透過CPU多核執行來處理。

### Geometry Processing 幾何處理
&emsp;&emsp;幾何相關，如變換、投影...等，此階段負責要繪製什麼、怎麼繪製、繪製在哪裡，通常由GPU執行。
&emsp;&emsp;逐頂點執行、逐三角形執行。此階段還會再細分成四個子階段。
![](/assets/imgs/Notes/RealTimeRendering/ch2/Geometry.png)

#### 頂點著色 Vertex Shading, 投影 Projecttion

&emsp;&emsp;用於計算頂點位置以及決定頂點資料如何輸出，如法線或貼圖座標。這個可程式化的頂點處理稱為 *頂點著色器 (vertex shader)* 。  
  
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
  
####  Optional Vertex Processing  
&emsp;&emsp;頂點處理完成後，有一些可選的行為可以執行。*Tessellation*, *Geometry Shading*, 和 *Stream Output*. 
* 曲面細分著色器 Tessellation
> &emsp;&emsp;問題: 用三角形填滿一顆球，如果數量少時，從遠處看沒問題，從近處看會有明顯輪廓。數量多時，浪費空間和效能。  
> &emsp;&emsp;目的: 用適合的數量填充物件。  
> &emsp;&emsp;頂點可以形成一個曲面，這個曲面包含一群補丁，每群補丁由頂點形成。*Tessellation* 包含 *hull shader*, *tessellator*, 和 *domain shader*，用來將一組頂點轉換成更多的頂點，形成新的三角形。並利用相機決定形成的數量，近則多，遠則少。 

* 幾何著色器 Geometry Shader
> &emsp;&emsp;比 *Tessellation* 更早出現，在 GPU 上常見。藉由輸入各種圖元，並產生新的頂點。用途之一為粒子模擬，假設一個煙火爆炸，每個火球可以用一個點表示，幾何著色器可以將點轉換為兩個三角形形成的正方形，並面向觀察者。

* Stream Output
> &emsp;&emsp;可以讓我們決定計算完的頂點要送到哪裡。除了直接往下一步流程送，我們可以存在陣列裡，並提供給 CPU 或 GPU 使用。

#### 剪裁 Clipping
&emsp;&emsp;只有位於相機可見範圍 *(view volume)* 中的圖元需要送到光柵化階段。  
&emsp;&emsp;完全在範圍中的物件會往下一階段送，完全不在範圍內的會直接捨棄，只有部分在範圍內，部分在範圍外的物件需要剪裁。  
&emsp;&emsp;剪裁使用由投影產生的四個值的齊次座標來執行，透視空間中的數值通常不會線性插值，因此第四個座標是需要的，以讓透視投影正確的插值和剪裁。最後執行透視除法 *perspective division*，將齊次座標的前三個值除以第四個值，得到設備標準三維座標 *normalized device coordinates*。
> &emsp;&emsp;齊次座標第四個值由投影產生，表示相機視錐體的剪裁範圍，因次用前三個座標 x, y, z 除以第四個值 w, 用來得到對應的座標。因直接使用視錐體計算邊界困難，所以需要 *canonical view volume* ，讓剪裁範圍為一個正方形，方便計算。 

![](/assets/imgs/Notes/RealTimeRendering/ch2/Clipping.png)   

#### 屏幕映射 Screen Mapping  
&emsp;&emsp;將三維座標轉換為螢幕二維座標。x, y座標為螢幕座標 *screen coordinates*, 螢幕座標加上 z 座標為 窗口座標 *window coordinates* 。
![](/assets/imgs/Notes/RealTimeRendering/ch2/ScreenMapping.png) 
> &emsp;&emsp;整數與浮點數和像素的關聯。  
![](/assets/imgs/Notes/RealTimeRendering/ch2/Formula.png)  
*d* 為整數， *c* 為浮點數，這個公式的目的在於讓像素對齊，確保圖像不會出現鋸齒或失真。

### Rasterization Stage 光柵化階段
### Pixel Processing 像素處理
 


