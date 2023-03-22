---
layout: single
title: Ch2. The Graphics Rendering Pipeline
date: 2023-03-08 14:20:29 +0800
excerpt: 筆記
categories:
- 筆記
tags:
- Render
- Note
mermaid: true
---

## 渲染管線 Rendering Pipeline
### 概覽
給予物件、光源、相機、繪製出圖形。
![](/assets/imgs/Notes/RealTimeRendering/ch2/CameraAndObjects.png)
* 展示相機顯示物件的示例圖，只有範圍(view volume)內的物件能夠看到。

![](/assets/imgs/Notes/RealTimeRendering/ch2/Pipeline.png)
* 渲染管線分為四個階段，每個階段還能各自有自己的Pipeline，即有更細的子階段。

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
> &emsp;&emsp;![](/assets/imgs/Notes/RealTimeRendering/ch2/Formula.png)  
> &emsp;&emsp;*d* 為整數， *c* 為浮點數，這個公式的目的在於讓像素對齊，確保圖像不會出現鋸齒或失真。

### Rasterization Stage 光柵化階段
&emsp;&emsp;找出所有要繪製的像素。又稱為 *掃描轉換(scan conversion)*  
&emsp;&emsp;分為兩個階段  
* 三角形設置 (triangle setup)
* 三角形遍歷 (triangle traversal)
> #### Triangle Setup  
> 這個階段會計算三角形的 *微分(differentials)* 與 *邊緣方程(edge equations)*，這些資料會用在 *triangle traversal* 中
> * 微分(differentials): 計算每個像素的位置變化，主要用來計算邊緣方程與插值因子。
> * 邊緣方程(edge equations): 用來計算三角形邊緣的方程式，這些方程式可以用來判斷一個像素是否在三角形內部，進而決定哪些像素需要進行顏色計算。
> * 插值因子(Interpolation): 用來計算每個像素的插值權重，這些權重可以用來進行各種插值運算，例如在三角形上進行紋理映射等操作。  

> #### Triangle Traversal
> 主要用來尋找哪個像素在三角形內。使用三角形的頂點進行內插，來產生一個fragment資料，例如顏色、深度等，最終，所有在三角形內部的像素或採樣點都會生成對應的Fragment，並被送往下一個階段的 *Pixel Processing* 進行後續的計算。

### Pixel Processing 像素處理
&emsp;&emsp;在這個階段所有像素被視為位於三角形內，並且會進行逐像素計算。
&emsp;&emsp;分為兩個階段
* 像素渲染(Pixel Shading)
* 合併 (Meging)
> #### 像素著色 (Pixel Shading)
> &emsp;&emsp;這裡會對所有像素進行渲染計算，使用來自於前一個階段送來的插值資料。這個階段會由程式化GPU核心計算，例如由程式設計師提供的 *fragment shader*。最後會有多個顏色送往下個階段。  
> &emsp;&emsp;這裡可以應用多個技術，其中之一為 *紋理計算(texturing)*，將多張圖片結合到一個物件當中，圖片可以是1, 2, 3維。
> &emsp;&emsp;![](/assets/imgs/Notes/RealTimeRendering/ch2/Texture.png) 

> #### 合併 (Meging)
> &emsp;&emsp;所有像素的資訊都儲存在 *顏色緩衝區 color buffer* 中，為一個顏色的陣列，每個顏色包含 紅、綠、藍。合併階段的功能在於將像素著色階段產生的片段(fragment)顏色與當前存儲在緩衝區中的顏色相結合。  
> &emsp;&emsp;這個階段又稱為 ROP, *raster operations (pipeline)* 或 *render output unit*  
> &emsp;&emsp;可見性處理，使用 *z-buffer* 或稱為 *深度緩衝區 depth buffer* 演算法，*z-buffer* 大小和形狀與 color buffer 一樣，儲存了每個像素最接近圖元的 *z值*，當一個圖元正在渲染到某個像素時，它在該像素上的 z值被計算並與 *z-Buffer* 中相應像素的 z值 進行比較。如果新的 z值比 z-buffer 中的 z值更小，那麼正在渲染的圖元比先前最靠近相機的圖元更接近相機。因此，該像素的 z值和顏色將被更新為正在繪製的圖元的 z值和顏色。如果計算出的Z值大於 z-buffer 中的 z值，則 color buffer 和 z-buffer 保持不變。
> &emsp;&emsp;*alpha channel* 儲存每個像素的不透明度值，在舊的API中，這個值用來根據 alpha測試 來捨棄像素。現今這個操作可以藉由程式完成，並且可以確保透明像素不影響 z-buffer。  
> &emsp;&emsp;*模板緩衝區 stencil buffer* 記錄已渲染的圖元的位置，每個像素有 8 個 bits。可以使用各種函數將圖元渲染到模板緩衝區中，然後可以使用緩衝區的內容來控制顏色緩衝區和深度緩衝區中的渲染。 例如，一個實心圓被繪製到模板緩衝區，可以與一個運算符號結合，使得只有圓存在的地方才可以將圖元繪製到顏色緩衝區中，達到 mask 效果。
> &emsp;&emsp; *幀緩衝區 framebuffer* 通常由所有緩衝區組合而成。
> &emsp;&emsp; 當一個圖元被送到光柵化階段，那些可見的點被繪製在螢幕上，為了避免人眼看到正在被光柵化並發送到螢幕的原始形狀，會使用 *雙緩衝區 double buffering*。這意味著場景的渲染是在 *後緩衝區(back buffer)* 中進行的。一旦場景在後緩衝區中渲染完成，後緩衝區的內容就會與之前顯示在螢幕上的 *前緩衝區(front buffer)* 的內容進行交換。交換通常發生在 *垂直消隱期間(vertical retrace)*，這是一個安全的時間點。