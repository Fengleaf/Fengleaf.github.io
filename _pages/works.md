---
title: "作品集"
layout: single
excerpt: "作品集"
sitemap: true
permalink: /works/
# header:
#   overlay_image: /assets/imgs/Taipei_Night.jpg
#   caption: "台北夜景"
author_profile: true
toc: true
---
內容目前正在施工中
{: .notice--danger}

## AI Projects
### Car Detection and Removal of Landscape Scene
`Mask-RCNN`, `Blender`, `2D to 3D Projection`  
建築業在做地景重建時常遇到重建出來的街道場景過多雜物，如：汽車、機車、破碎號誌竿以及樹木等。因此在重建前，先透過深度學習(Mask-RCNN) 產生這些雜物的 Mask，在重建時就不會將這些空拍圖車輛的 Pixel 資訊納入重建。得以產出乾淨的 3D 街道場景。我們透過結合深度學習與 3D 重建的相機資訊，得以反推出 3D 場景中所有車輛的 Mask，達到幾乎 99% 的辨識率。

<figure>
	<a href="/assets/imgs/Projects/Software/AI/110_%E5%9C%B0%E6%99%AF%E8%BB%8A%E8%BC%9B%E5%B0%88%E9%A1%8C%E6%B5%B7%E5%A0%B1.jpg"><img src="/assets/imgs/Projects/Software/AI/110_%E5%9C%B0%E6%99%AF%E8%BB%8A%E8%BC%9B%E5%B0%88%E9%A1%8C%E6%B5%B7%E5%A0%B1.jpg"></a>
</figure>

## Graphics Projects
### Incremental Instant Radiosity
`Unity Engine`, `Ray Tracing`, `Radiosity`  
> [https://github.com/ImRTon/Incremental-Instant-Radiosity](https://github.com/ImRTon/Incremental-Instant-Radiosity)

<figure>
	<a href="/assets/imgs/Projects/Software/3DGame/IncrementalInstantRadiosity.jpg"><img src="/assets/imgs/Projects/Software/3DGame/IncrementalInstantRadiosity.jpg"></a>
</figure>

### Interactive PailouModeling
`Unity Engine`, `Procedure Modeling`  

> [https://github.com/ImRTon/InteractivePailouModeling](https://github.com/ImRTon/InteractivePailouModeling)

<figure>
	<a href="/assets/imgs/Projects/Software/3DGame/InteractivePailouModeling.jpg"><img src="/assets/imgs/Projects/Software/3DGame/InteractivePailouModeling.jpg"></a>
</figure>

### 2D to 3D Maze
`OpenGL`, `Visible-Surface Determination`  
使用OpenGL 將左圖的2D 迷宮轉換成3D 版本，牆壁、顏色等都要自己計算，包含要畫在畫面上的哪個位置，且不能套用Z-Buffer 等OpenGL 3D 函式。  
<figure>
	<a href="/assets/imgs/Projects/Software/Graphics/Maze.png"><img src="/assets/imgs/Projects/Software/Graphics/Maze.png"></a>
</figure>

## VFX Projects
### HighDynamicRange-Imaging
`Image Alignment using MTB`, `Solve Response Curve`, `Radiance Map`, `Tone Mapping`  
Calculate HDR image using multi-exposure images.  
> [https://github.com/ImRTon/HighDynamicRange-Imaging](https://github.com/ImRTon/HighDynamicRange-Imaging)  

<figure>
	<a href="https://github.com/ImRTon/HighDynamicRange-Imaging/raw/master/results/Elephant_mountain_101/ldr_0.2.jpg"><img src="https://github.com/ImRTon/HighDynamicRange-Imaging/raw/master/results/Elephant_mountain_101/ldr_0.2.jpg"></a>
</figure>

### Image Stitching  
`SIFT`  
Combine a set of images into a larger image by registering, warping, resampling and blending them together.  
> [https://github.com/ImRTon/VFX-ImageStitching](https://github.com/ImRTon/VFX-ImageStitching)  

<figure>
	<a href="https://camo.githubusercontent.com/b872e03c94790592cde0ee77c5e76e41677c6dbbbc393d356060624c2d718fac/68747470733a2f2f696d6775722e636f6d2f4462434b4d6b612e6a7067"><img src="https://camo.githubusercontent.com/b872e03c94790592cde0ee77c5e76e41677c6dbbbc393d356060624c2d718fac/68747470733a2f2f696d6775722e636f6d2f4462434b4d6b612e6a7067"></a>
</figure>

## Game Projects

### Tank Battle
`Unity Engine`, `Third Person`
<figure>
	<a href="/assets/imgs/Projects/Software/Game/TankBattle.jpg"><img src="/assets/imgs/Projects/Software/Game/TankBattle.jpg"></a>
</figure>

### Zombie Field 2022 - First Person Shooter
`Unreal Engine 4`, `FPS`
<figure class="half">
    <a href="/assets/imgs/Projects/Software/Game/ZombieField_1.png"><img src="/assets/imgs/Projects/Software/Game/ZombieField_1.png"></a>
    <a href="/assets/imgs/Projects/Software/Game/ZombieField_2.png"><img src="/assets/imgs/Projects/Software/Game/ZombieField_2.png"></a>
</figure>

## Hardware Projects

### DIY Electric Scooter v4
`3D Printing`, `Electric Circuit`, `Motor Control`, `Lithium Battery`  
改造自迪卡農滑板車 Town 7 XL，為一台輕量化可攜式的電動輔助滑板車，可折疊，全車重 < 8kg，易於攜帶。配有可抽換式電池，方便充電以及快速的電力補充。

<figure class="half">
    <a href="/assets/imgs/Projects/Hardware/ElectricScooterV4/P_20210821_160935.jpg"><img src="/assets/imgs/Projects/Hardware/ElectricScooterV4/P_20210821_160935.jpg"></a>
    <a href="/assets/imgs/Projects/Hardware/ElectricScooterV4/P_20210821_160948.jpg"><img src="/assets/imgs/Projects/Hardware/ElectricScooterV4/P_20210821_160948.jpg"></a>
    <figcaption>電動滑板車</figcaption>
</figure>

### Battery Pack of Electric Scooter
`3D Printing`, `Electric Circuit`, `Lithium Battery`  
電動滑板車的電池組，由 6 顆 Sanyo NCR-20700B 鋰離子電池組成 12V 的電池系統，可外接 30W PD 充電器與 DC-DC 轉換電路，可提供 60W PD 輸出或是 20W Dash/VOOC 輸出。

![](/assets/imgs/Projects/Hardware/BatteryPackV1/P_20210821_161144.jpg)

### 3D Printed KartRider Race Car
`3D Printing`, `3D Modeling`  
透過 Autodesk Inventor 去建出跑跑卡丁車中的積木舒適 9 卡丁車。  

<figure class="third">
    <a href="/assets/imgs/Projects/Hardware/CarModel/CarFront.jpg"><img src="/assets/imgs/Projects/Hardware/CarModel/CarFront.jpg"></a>
    <a href="/assets/imgs/Projects/Hardware/CarModel/CarBack.jpg"><img src="/assets/imgs/Projects/Hardware/CarModel/CarBack.jpg"></a>
    <a href="/assets/imgs/Projects/Hardware/CarModel/CarRendered.png"><img src="/assets/imgs/Projects/Hardware/CarModel/CarRendered.png"></a>
</figure>

### 準時睡鬧鐘 (黑客松)
`Arduino`, `24 Hours`, `黑客松`, `MakeNTU`  

準時睡鬧鐘有幾項特點：  
* 睡覺時間會響起鬧鈴，直到將手機放入充電  
* 於睡覺時間，無法打開拿取手機  
* 如有急迫需求使用手機，只能使用畫面上下顛倒，操作左右相反的觸碰筆  
* 從發想到實作皆於24 小時內  

在這聯網時代，手機變成什麼都可以控制，然而我們也捫心自問，手機是除了方便了生活，是否卻不方便了準時上床睡覺呢? 為了維持正常睡覺的習慣，為了不再因為一個通知而在床上划手機還不小心熬夜，這個鬧鐘會在設定的睡覺時間響起，提醒你該交出手機去睡覺，若還不照做就會一直吵你，還越來越吵煩到你受不了乖乖交出手機。怎麼知道你真的放下手機了? 這就得提到很多人使用手機的習慣⸺睡前充電，我們透過感測是否有充電，排除掉放入鬧鐘裡的東西不是手機。當放入手機並且按下右側睡覺鈕後，手機將會被關起來直到隔天設定的起床鬧鐘響起，才會退出來還給使用者。  
在這個聯網時代，希望藉由這個「準時睡鬧鐘」，讓人們學習享受便利的同時，不會被手機所綁架而不方便最基本的睡眠。  
<figure>
	<a href="/assets/imgs/Projects/Hardware/SleepNowAlarmClock/Clock.jpg"><img src="/assets/imgs/Projects/Hardware/SleepNowAlarmClock/Clock.jpg"></a>
</figure>

### 動物森林 (與設計系合作之桌遊)
`Arduino`, `3D Printing`, `3D Modeling`  
這是幫助設計系同學專題的作品，跳過遊戲規則以及概念設計部分。總之這是一款想要藉由卡牌，結合聲音互動的遊戲。主要的機電部分就是上方這顆方形的主機，透過感應卡片的RFID 辨認動物類別，在使用者壓下卡片時，會使這個裝置發出那個動物的叫聲，藉以使孩童去認識動物。當中設計系的同學提供這個產品的遊戲概念以及美觀部分，我與另一位電機系同學負責將機電部分實作與概念優化。因而最後產出了這個作品。  

<figure class="third">
    <a href="/assets/imgs/Projects/Hardware/ForestBoardGame/AnimalForest_1.jpg"><img src="/assets/imgs/Projects/Hardware/ForestBoardGame/AnimalForest_1.jpg"></a>
    <a href="/assets/imgs/Projects/Hardware/ForestBoardGame/AnimalForest_2.jpg"><img src="/assets/imgs/Projects/Hardware/ForestBoardGame/AnimalForest_2.jpg"></a>
    <a href="/assets/imgs/Projects/Hardware/ForestBoardGame/Circuit.jpg"><img src="/assets/imgs/Projects/Hardware/ForestBoardGame/Circuit.jpg"></a>
</figure>  
<figure>
	<a href="/assets/imgs/Projects/Hardware/ForestBoardGame/AnimalForest_3.jpg"><img src="/assets/imgs/Projects/Hardware/ForestBoardGame/AnimalForest_3.jpg"></a>
</figure>